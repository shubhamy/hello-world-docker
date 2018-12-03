FROM python:2.7.10

# RUN apt-get update && \
#     apt-get -y upgrade && \
#     apt-get -y autoremove

RUN mkdir /data
ADD requirements.txt /data

WORKDIR /data
RUN pip install --upgrade pip && \
    pip install -r requirements.txt && \
    rm requirements.txt

EXPOSE 3306

ENTRYPOINT [ "python" ]