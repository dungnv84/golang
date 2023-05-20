#  docker runner
  name = "MyKiot Development Environment"
  url = "https://gitlab.citigo.com.vn"
  token = "ZAmbH39m3ERLTVVobjTG"
  executor = "docker"
  limit = 3
  request_concurrency = 3
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "docker:stable"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
    shm_size = 0

---
# File /var/run/docker.sock cho phép các ứng dụng trên máy chủ host (hoặc trong các container khác) giao tiếp với Docker daemo    

nó sử dụng file socket của docker host để tương tác vs docker daemon của host
1 số phiên bản của docker khi cài trên host ko hỗ trợ buildkit thì ta sẽ đóng 1 docker khác để có buildx
--- 
# dockerfile 
FROM docker:stable
RUN mkdir -p ~/.docker/cli-plugins && \
    cd ~/.docker/cli-plugins && \
    wget https://github.com/docker/buildx/releases/download/v0.6.1/buildx-v0.6.1.linux-amd64 && \
    chmod a+x buildx-v0.6.1.linux-amd64 && \
    mv buildx-v0.6.1.linux-amd64 docker-buildx && \
    docker buildx install