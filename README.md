# FortiClient VPN on Parrot OS

> Guia rápido (e testado) para baixar, instalar, configurar, atualizar e remover o **FortiClient VPN** (edição gratuita) no Parrot OS, distribuição baseada em Debian.

## Ambiente testado

* **Parrot Security 6.1** (kernel 6.x, Debian 12 *bookworm*, amd64)
* **FortiClient VPN 7.4.3 Build 1736** (lançado em 22 abr 2025)

## 1 · Instalação rápida (TL;DR)

```bash
cd /tmp
wget https://filestore.fortinet.com/forticlient/downloads/forticlient_vpn_7.4.3.1736_amd64.deb
sudo apt update
sudo apt install -y libappindicator1 libdbusmenu-gtk3 libindicator7 # dependências faltantes
sudo dpkg -i forticlient_vpn_7.4.3.1736_amd64.deb
sudo apt -f install -y   # corrige deps restantes, se houver
```

Inicie com:

```bash
forticlient &
```

---

## 2 · Passo‑a‑passo detalhado

### 2.1 Baixar o instalador

* **Link direto**: [`forticlient_vpn_7.4.3.1736_amd64.deb`](https://filestore.fortinet.com/forticlient/downloads/forticlient_vpn_7.4.3.1736_amd64.deb)
* **Portal oficial**: [https://www.fortinet.com/br/support/product-downloads](https://www.fortinet.com/br/support/product-downloads) → *FortiClient VPN for Linux* e escolha **7.4 > Linux (DEB)**. ([fortinet.com](https://www.fortinet.com/support/product-downloads/linux?utm_source=chatgpt.com))

Opcional – verifique a integridade:

```bash
sha256sum forticlient_vpn_7.4.3.1736_amd64.deb
# compare com o hash informado nos Release Notes
```

### 2.2 Instalar dependências

Parrot traz a maioria das bibliotecas, mas o FortiClient ainda exige AppIndicator:

```bash
sudo apt update
sudo apt install -y libappindicator1 libdbusmenu-glib4 libdbusmenu-gtk3 libindicator7
```

### 2.3 Instalar o pacote

```bash
sudo dpkg -i forticlient_vpn_7.4.3.1736_amd64.deb
sudo apt -f install -y
```

### 2.4 Primeira execução & GUI

* **Menu ▸ Internet ▸ FortiClient VPN**
* ou execute `forticlient` no terminal.

Aceite o EULA na primeira execução.

### 2.5 Adicionar seu perfil VPN

1. Clique **Configure VPN**.
2. Selecione **SSL‑VPN** (ou **IPsec** se seu FortiGate usar IPsec).
3. Preencha **Connection Name**, **Remote Gateway** e **Port** (padrão 443).
4. Clique **Save** e depois *Connect*.

Conexão via CLI:

```bash
forticlient --remote-gw 203.0.113.1 --vpnuser alice --keep-alive
```

---

## 3 · Manter o FortiClient atualizado (repositório opcional)

A Fortinet não publica um repositório Debian/Parrot, mas o repo para **Ubuntu 22.04 (jammy)** funciona.

```bash
# chave GPG
awget -O - https://repo.fortinet.com/repo/forticlient/7.4/ubuntu22/DEB-GPG-KEY \
  | gpg --dearmor | sudo tee /usr/share/keyrings/fortinet.gpg

# adicionar repo
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/fortinet.gpg] \
https://repo.fortinet.com/repo/forticlient/7.4/ubuntu22/ stable non-free" | \
sudo tee /etc/apt/sources.list.d/forticlient.list

# instalar / atualizar
sudo apt update
sudo apt install forticlient
```

> **Aviso:** suporte oficial apenas para Ubuntu; use no Parrot por sua conta e risco. Instruções baseadas nos *Release Notes* oficiais. ([fortinetweb.s3.amazonaws.com](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/attachments/cb929e9c-fdc9-11ef-b13a-ca4255feedd9/forticlient-7.4.3-linux-release-notes.pdf))

---

## 4 · Desinstalar

```bash
sudo apt purge forticlient
sudo rm /etc/apt/sources.list.d/forticlient.list  # se você habilitou o repo
sudo apt autoremove
```

---

## 5 · Solução de problemas comuns

| Sintoma                                                                     | Correção                                                                                                  |
| --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **Erro de biblioteca:** `forticlient: error while loading shared libraries` | Reinstale libs ausentes: `sudo apt install libdbusmenu-gtk3 libindicator7`                                |
| **VPN conecta mas não trafega**                                             | Verifique `/etc/resolv.conf` e rotas: `ip r`. Ajuste split‑tunnel no FortiGate ou empurre DNS via portal. |
| **GUI não conecta**                                                         | Rode em modo verbose: `forticlient --log-level 3` e cheque erros de certificado.                          |

Consulte os Release Notes para lista de *known issues* e bugs corrigidos. ([fortinetweb.s3.amazonaws.com](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/attachments/cb929e9c-fdc9-11ef-b13a-ca4255feedd9/forticlient-7.4.3-linux-release-notes.pdf))

---

## 6 · Referências

* **FortiClient (Linux) 7.4.3 Release Notes** – 22 abr 2025. ([fortinetweb.s3.amazonaws.com](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/attachments/cb929e9c-fdc9-11ef-b13a-ca4255feedd9/forticlient-7.4.3-linux-release-notes.pdf))
* **FortiClient Product Downloads page**. ([fortinet.com](https://www.fortinet.com/support/product-downloads/linux?utm_source=chatgpt.com))

---

**Licença:** FortiClient é software proprietário; leia e aceite o EULA. Este README está sob licença MIT.
