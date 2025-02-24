FROM netdata/netdata:v1.35
LABEL org.opencontainers.image.source https://github.com/kiwix/container-images

ENV DO_NOT_TRACK 1
# CIDRs to allow regarless of whether in zimfarm API. Separate with spaces
ENV STREAM_WHITELIST ""
ENV NETDATA_HOSTNAME monitoring.openzim.org
ENV STREAM_KEY 7DC0DDB8-8EAA-4391-AF5D-BE017EECF0EE

COPY netdata.conf /etc/netdata/netdata.conf
COPY update-stream-whitelist.sh /etc/periodic/15min/update-stream-whitelist
COPY regen-stream-conf.py /usr/local/bin/regen-stream-conf
COPY entrypoint.sh /usr/local/bin/entrypoint

# disable all external modules
RUN python3 -m pip install requests \
&& printf "enabled: no\n" | tee /etc/netdata/python.d.conf /etc/netdata/go.d.conf /etc/netdata/node.d.conf \
&& printf "#!/bin/bash\nrm -rf /var/cache/netdata/* && kill 1\n" > /usr/local/bin/reset-netdata \
&& chmod a+x /usr/local/bin/reset-netdata \
&& chmod a+x /usr/local/bin/regen-stream-conf \
&& chmod a+x /etc/periodic/15min/update-stream-whitelist \
&& touch /etc/netdata/.opt-out-from-anonymous-statistics \
&& echo "@reboot /etc/periodic/15min/update-stream-whitelist" >> /etc/crontabs/root \
&& touch /etc/netdata/stream.conf \
&& chmod a+x /usr/local/bin/entrypoint \
&& wget https://github.com/Yelp/dumb-init/releases/download/v1.2.5/dumb-init_1.2.5_x86_64 \
&& mv dumb-init_1.2.5_x86_64 /usr/bin/dumb-init \
&& chmod +x /usr/bin/dumb-init

ENTRYPOINT ["/usr/bin/dumb-init", "--", "entrypoint"]
