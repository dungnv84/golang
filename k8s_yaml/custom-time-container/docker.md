FROM ubuntu:latest

COPY set-time.sh /usr/local/bin
RUN chmod +x /usr/local/bin/set-time.sh && \
    apt-get update && \
    apt-get install -y tzdata
RUN set-time.sh
CMD ["/bin/bash"]

apt-get install -y tzdata && \: Cài đặt gói tzdata để cho phép chỉnh sửa múi giờ.

# file set-time.sh

#!/bin/bash
cp /etc/localtime /tmp/localtime
date --set="$(date --date='15 minutes ago' +'%Y-%m-%d %H:%M:%S')"
cp /tmp/localtime /etc/localtime


Dòng đầu tiên (#!/bin/bash) khai báo rằng tệp shell này sẽ được thực thi bởi Bash.
cp /etc/localtime /tmp/localtime: Sao chép tệp /etc/localtime chứa thông tin về múi giờ hiện tại vào tệp /tmp/localtime.
