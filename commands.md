first lab:

cd /kickstart
ls -lh
sudo apt-get install -y python3 python3-pip
pip3 install tornado
python3 web-server.py &
curl http://localhost:8888
kill %1
cat Dockerfile
sudo docker build -t py-web-server:v1 .
Run: sudo docker run -d -p 8888:8888 --name py-web-server -h my-web-server py-web-server:v1
Try accessing the web server again, and then stop the container.
curl http://localhost:8888
sudo docker rm -f py-web-server
The web server and all its dependencies, including the python and tornado library, have been packaged into a single Docker image that can now be shared with