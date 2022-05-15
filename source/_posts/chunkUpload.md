---
title: 配合antd上传组件的分片上传实现
date: 2020-09-14 18:13:47
tags: 分片上传
categories: 前端
---

## 分片上传

项目中遇见了超过 1G 的大文件，在上传接口调用的时候会超时，报错。
为了解决问题，搞了个分片上传，配合 antd 的 Upload 组件
主要是利用了 Blob 文件的 blob.slice 方法切割文件
然后把切割出来的文件传给后端

```
            <Upload
              action={`/server/api/orderFiles/cus/${order.id}/enable/1/dipServices/${order.dipService?.id}`}
              autoUpload={false}
              multiple
              beforeUpload={(file, fileList) => {
                if (file.size <= 0) {
                  message.warning(formatMessage({ id: '文件无效，请重新上传!' }));
                  return false;
                }
                if (fileList.length > 10 - originFiles.length && fileList.indexOf(file) === 0) {
                  message.warning(formatMessage({ id: '选择文件数量超出限制，请重新选择' }));
                  return false;
                }
                if (fileList.length > 10 - originFiles.length) {
                  return false;
                }
                if (originFiles.length > 10) {
                  message.warning(formatMessage({ id: '已上传文件数量超过限制，无法上传' }));
                  return false;
                }

                // if (file.size > 1024 * 1024 * 5) {
                //   message.warning(formatMessage({ id: `文件：${file.name} 超过5M，无法上传!` }));
                //   return false;
                // }
                return true;
              }}
              customRequest={({ file, onError, onSuccess }) => {
                //  如果小于5M 上传整个文件，如果超过5M分片上传
                const bytesPerPiece = 1024 * 1024 * 5; // 每个文件切片大小定为5MB .
                // const uid = uuid.v4();
                if (file.size < bytesPerPiece) {
                  const formData = new FormData();
                  formData.append('file', file);
                  formData.append('chunkFlag', false);
                  formData.append('chunk', 0); //  第几片
                  formData.append('chunkSize', bytesPerPiece); //  分片大小
                  formData.append('chunks', 1); // 分片总数
                  formData.append('uuid', file.uid); //  整个文件的标识

                  request(
                    `/server/api/orderFiles/cus/${order.id}/enable/1/dipServices/${order.dipService?.id}`,
                    {
                      method: 'post',
                      body: formData,
                    },
                  ).then(res => {
                    if (res.success) {
                      onSuccess(res, file);
                    } else {
                      onError();
                    }
                    getOriginFiles({
                      id: order.id,
                    });
                  });
                  return;
                }
                // 发送请求

                const blob = new Blob([file]);
                let start = 0;
                let end;
                let index = 0;
                const filesize = blob.size;
                // const filename = blob.name;
                // 计算文件切片总数
                const totalPieces = Math.ceil(filesize / bytesPerPiece);
                const filePieces = [];
                while (start < filesize) {
                  end = start + bytesPerPiece;
                  if (end > filesize) {
                    end = filesize;
                  } // 匹配最后一个分片的情况

                  const chunk = blob.slice(start, end); // 切割文件
                  const sliceIndex = file.name;
                  const formData = new FormData();
                  formData.append('file', chunk, sliceIndex);
                  formData.append('chunk', index); //  第几片
                  formData.append('chunkSize', bytesPerPiece); //  分片大小
                  formData.append('chunks', totalPieces); // 分片总数
                  formData.append('chunkFlag', true); //  是否分片
                  formData.append('uuid', file.uid); //  整个文件的标识
                  filePieces.push(formData);
                  // request(
                  //   `/server/api/orderFiles/cus/${order.id}/enable/1/dipServices/${order.dipService?.id}`,
                  //   {
                  //     method: 'post',
                  //     body: formData,
                  //   },
                  // ).then(() =>
                  //   getOriginFiles({
                  //     id: order.id,
                  //   }),
                  // );

                  start = end;
                  index += 1;
                }

                Promise.all(
                  filePieces
                    .filter((item, arrayIndex, array) => {
                      return arrayIndex < array.length - 1;
                    })
                    .map(item => {
                      return request(
                        `/server/api/orderFiles/cus/${order.id}/enable/1/dipServices/${order.dipService?.id}`,
                        {
                          method: 'post',
                          body: item,
                        },
                      );
                    }),
                ).then(() => {
                  request(
                    `/server/api/orderFiles/cus/${order.id}/enable/1/dipServices/${order.dipService?.id}`,
                    {
                      method: 'post',
                      body: filePieces[filePieces.length - 1],
                    },
                  ).then(res => {
                    if (res.success) {
                      onSuccess(res, file);
                    } else {
                      onError();
                    }
                    getOriginFiles({
                      id: order.id,
                    });
                  });
                });
              }}
              // onAllComplete={() => {

              // }}
            />

```
