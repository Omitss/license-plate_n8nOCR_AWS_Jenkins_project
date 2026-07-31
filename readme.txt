docker build -t my-jenkins -f Dockerfile.jenkins .

docker run -d `
  --name jenkins `
  -p 8080:8080 `
  -p 50000:50000 `
  -v jenkins_home:/var/jenkins_home `
  -v //var/run/docker.sock:/var/run/docker.sock `
  my-jenkins
  >>1a0a9f490fde7d918109961c2c152359a9c04ef8243a2c41c76d90a9be8dcbf1

Jenkins 초기 비밀번호
98828c545d3c409ea0b413572ed12076

jenkins 사용자를 root 그룹에 추가
docker exec -u root jenkins usermod -aG root jenkins