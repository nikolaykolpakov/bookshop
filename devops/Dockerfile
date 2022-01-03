FROM python:3.9.9-slim as builder

ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1

WORKDIR /tmp/

COPY requirements.txt .

WORKDIR /tmp/wheels

RUN pip wheel --no-cache-dir --no-deps --wheel-dir . -r /tmp/requirements.txt


FROM python:3.9.9-slim

# без C_FORCE_ROOT=true celery не запускает из под рута
ENV C_FORCE_ROOT=true \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \

# Сервис работает по Московскому времени
RUN echo "Europe/Moscow" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata


COPY --from=builder /tmp/wheels /tmp/wheels

RUN pip install --no-cache /tmp/wheels/*

WORKDIR /srv

COPY . .

RUN python manage.py collectstatic

CMD  ['/bin/sh','-c','python manage.py migrate --noinput && python cherry.py']
