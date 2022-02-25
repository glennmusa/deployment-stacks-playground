FROM mcr.microsoft.com/powershell:lts-debian-11

# [Option] Install zsh
ARG INSTALL_ZSH="true"
# [Option] Upgrade OS packages to their latest versions
ARG UPGRADE_PACKAGES="false"
# [Option] Install Azure PowerShell
ARG REPOSITORY=PSGallery
ARG MODULE=Az
ARG CONFIG=config
ARG VERSION=7.2.0

# Install needed packages and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install PowerShell
COPY library-scripts/common-debian.sh /tmp/library-scripts/
RUN apt-get update \
    && /bin/bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" "true" "true" \
    && chsh "${USERNAME}" -s "$(which pwsh)" \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# Install Azure PowerShell Module
RUN pwsh -Command Set-PSRepository -Name ${REPOSITORY} -InstallationPolicy Trusted && \
    pwsh -Command Install-Module -Name ${MODULE} -RequiredVersion ${VERSION} -Scope AllUsers -Repository ${REPOSITORY} && \
    pwsh -Command Set-PSRepository -Name ${REPOSITORY} -InstallationPolicy Untrusted

# Install Azure CLI and Bicep
COPY library-scripts/azcli-debian.sh /tmp/library-scripts/
RUN bash /tmp/library-scripts/azcli-debian.sh \
    && curl -Lo /usr/local/bin/bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64 \
    && chmod +x /usr/local/bin/bicep \
    && apt-get clean -y && rm -rf /var/lib/apt/lists/* /tmp/library-scripts

# [Optional] Uncomment this section to install additional packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends <your-package-list-here>
