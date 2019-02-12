---
title: Přechod z podepsaného svým držitelem na veřejné certifikáty certifikační Autority pro P2S brány | Azure VPN Gateway | Dokumentace Microsoftu
description: Tento článek pomáhá úspěšně přejít do nové veřejné certifikáty certifikační Autority pro P2S brány.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: a5634054746d7e60aff154165f651f7c815c39a3
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100818"
---
# <a name="transition-from-self-signed-to-public-ca-certificates-for-p2s-gateways"></a>Přechod z podepsaného svým držitelem do veřejné certifikáty certifikační Autority pro P2S brány

Azure VPN Gateway už vydává certifikáty podepsané svým držitelem pro brány pro připojení P2S. Vystavené certifikáty jsou podepsané nyní podle veřejnou certifikační autoritu (CA). Nicméně starší brány může stále používat certifikáty podepsané svým držitelem. Tyto certifikáty podepsané svým držitelem se mu blížíte jejich data vypršení platnosti a musí být převeden do veřejné certifikáty certifikační Autority.

Dříve certifikát podepsaný svým držitelem pro bránu je potřeba aktualizovat každý 18 měsíců. Konfiguračních souborů klienta VPN pak musel být generovány a znovu nasadit na všechny klienty P2S. Přechod na veřejné certifikáty certifikační Autority se vyloučí toto omezení. Kromě přechodu pro certifikáty poskytuje tato změna také vylepšení platformy, lepší metriky a vylepšení stability.

Touto změnou jsou ovlivněny pouze starší brány. Pokud váš certifikát brány převedou, zobrazí se komunikace nebo informační zprávy na webu Azure Portal. Můžete zkontrolovat, pokud vaše brána je ovlivněno pomocí kroků v tomto článku.

>[!IMPORTANT]
>Tento přechod je naplánováno Březnových aktualizací 12,2019 od 18:00 UTC. Pokud dáváte přednost jiný časový interval, můžete vytvořit lístek podpory. Požádat o jednu z následujících oken:
>
>* 06:00 UTC na 25 února
>* 18:00 UTC na 25 února
>* 06:00 UTC na 1 března
>* 18:00 UTC na 1 března
>
>**Všechny zbývající brány přejde na 12 března. května 2019 od 18:00 UTC**.
>

## <a name="1-verify-your-certificate"></a>1. Ověřte váš certifikát

1. Zaškrtněte, pokud chcete zobrazit, pokud jsou ovlivněny této aktualizace. Stáhněte si pomocí postupu v aktuální konfiguraci klienta VPN [v tomto článku](point-to-site-vpn-client-configuration-azure-cert.md).

2. Otevřete nebo extrahování souboru zip a přejděte do složky "Generic". V obecné složce, zobrazí se dva soubory, z nichž jeden je *VPNSettings.xml*.
3. Otevřít *VPNSettings.xml* v libovolném prohlížeči nebo editoru xml. V souboru xml vyhledejte následující pole:

  * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
  * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Pokud *ServerCertRotCn* a *ServerCertIssuerCn* jsou "DigiCert globální kořenové certifikační Autority", nejsou touto aktualizací ovlivněny a není nutné postupujte podle kroků v tomto článku. Pokud ale zobrazí se něco jiného, certifikát brány je v rámci aktualizace a se převedou.

## <a name="2-check-certificate-transition-schedule"></a>2. Ověření plánu přechodu certifikátu

Pokud váš certifikát není v rámci aktualizace, váš certifikát brány se převedou. Odkazovat **důležité** Poznámka pro časů přechodů. Po aktualizaci P2S klienti nebudou moct připojit pomocí jejich starý profil. Musíte vygenerovat nový klient profily sítě VPN a nainstalovat na klientských počítačích.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Vytvořit profil konfigurace klienta VPN

Po certifikátu byla převedena, stáhněte si nový profil VPN (konfiguračních souborů klienta VPN) na webu Azure Portal. Pokyny najdete v tématu [vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md). Nemusíte generovat nový klientské certifikáty.

## <a name="4-deploy-vpn-client-profile"></a>4. Nasadit profil klienta VPN

Nasaďte nový profil na všechny klienty VPN typu point-to-site. Klienti VPN dojde ke ztrátě připojení, dokud se nový profil sítě VPN pro připojení point-to-site je stažen a nasadí na klientská zařízení. Klientské certifikáty, které jsou již nainstalovány na počítače klienta VPN není nutné ho znovu vystavit.

## <a name="5-connect-the-vpn-client"></a>5. Připojení klienta VPN

Připojení k Azure z klienta VPN jako obvykle.
