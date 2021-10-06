# Instruction FROM : image parente
FROM debian:buster-slim

# MEtadata LABEL (visible dans inspect ou registry)
LABEL maintainer="Pierre <psable@dawan.fr>"

RUN useradd --create-home --shell /bin/bash app_user

WORKDIR /home/app_user

COPY --chown=app_user script_appli.sh .

USER app_user

CMD ["bash", "script_appli.sh"]