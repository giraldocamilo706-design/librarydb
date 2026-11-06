FROM mcr.microsoft.com/devcontainers/java:25-bookworm

# La imagen base trae una fuente apt de Yarn sin clave GPG vigente (NO_PUBKEY 62D54FD4003F6525).
# Hace fallar `apt-get update` al instalar features como docker-in-docker; se elimina antes.
RUN rm -f /etc/apt/sources.list.d/yarn.list \
    && find /etc/apt -type f \( -name '*.list' -o -name '*.sources' \) -exec grep -l 'dl.yarnpkg.com' {} + 2>/dev/null | xargs -r rm -f
