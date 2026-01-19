FROM quay.io/frrouting/frr:10.5.1

COPY docker-start.sh /usr/lib/frr/docker-start
RUN chmod +x '/usr/lib/frr/docker-start'

ENTRYPOINT ["/sbin/tini", "--"]
CMD ["/usr/lib/frr/docker-start"]
