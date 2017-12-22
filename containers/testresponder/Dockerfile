FROM python:2.7.14-alpine3.6

RUN pip install tornado
COPY application.py /application.py
COPY start_services.sh /start_services.sh
RUN chmod +x /start_services.sh

ENTRYPOINT /start_services.sh