---
title: Přechod na veřejné certifikáty certifikační Autority pro P2S brány | Azure VPN Gateway | Dokumentace Microsoftu
description: Tento článek pomáhá úspěšně přejít do nové veřejné certifikáty certifikační Autority pro P2S brány.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 03/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.openlocfilehash: 29f2aeee53e07adfeafb8017c489c0b830f24b36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859643"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Přechod na certifikát veřejné brány CA pro P2S

Azure VPN Gateway už vydává certifikáty podepsané svým držitelem úrovně Azure pro své brány pro připojení P2S. Vystavené certifikáty jsou podepsané nyní podle veřejnou certifikační autoritu (CA). Nicméně některé starší brány může stále používat certifikáty podepsané svým držitelem. Tyto certifikáty podepsané svým držitelem se mu blížíte jejich data vypršení platnosti a musí být převeden do veřejné certifikáty certifikační Autority.

>[!NOTE]
> * Certifikáty podepsané svým držitelem použit pro ověření klienta P2S neovlivní tato změna certifikátu úrovně Azure. Můžete pokračovat k vystavování a použití certifikátů podepsaných svým držitelem jako za normálních okolností.
>

Certifikáty v tomto kontextu jsou další úrovně Azure certifikát. Nejsou řetězy certifikátů, které používáte při vytváření vlastní kořenovými certifikáty podepsanými svým držitelem a klientských certifikátů pro ověřování. Tyto certifikáty zůstanou beze změny a dnů do vypršení platnosti dat je k tomu vygeneroval.

Certifikát podepsaný svým držitelem pro bránu (vydané na pozadí Azure) dříve, potřeba aktualizovat každý 18 měsíců. Konfiguračních souborů klienta VPN pak musel být generovány a znovu nasadit na všechny klienty P2S. Přechod na veřejné certifikáty certifikační Autority se vyloučí toto omezení. Kromě přechodu pro certifikáty poskytuje tato změna také vylepšení platformy, lepší metriky a vylepšení stability.

Touto změnou jsou ovlivněny pouze starší brány. Pokud váš certifikát brány převedou, zobrazí se komunikace nebo informační zprávy na webu Azure Portal. Můžete zkontrolovat, pokud vaše brána je ovlivněno pomocí kroků v tomto článku.

> [!IMPORTANT]
> Tento přechod je naplánováno 12 března. května 2019 od 18:00 UTC. Pokud dáváte přednost jiný časový interval, můžete vytvořit lístek podpory. Ujistěte se a dokončit váš požadavek předem nejméně 24 hodin.  Požádat o jednu z následujících oken:
>
> * 06:00 UTC na 25. února
> * 18:00 UTC na 25. února
> * 06:00 UTC 1. března
> * 18:00 UTC 1. března
>
> **Všechny zbývající brány přejde na 12 března. května 2019 od 18:00 UTC**.
>
> Zákazníci obdrží e-mailu při jejich brány dokončí proces přechodu.
> 

## <a name="1-verify-your-certificate"></a>1. Ověřte váš certifikát

### <a name="resource-manager"></a>Resource Manager

1. Zaškrtněte, pokud chcete zobrazit, pokud jsou ovlivněny této aktualizace. Stáhněte si pomocí postupu v aktuální konfiguraci klienta VPN [v tomto článku](point-to-site-vpn-client-configuration-azure-cert.md).

2. Otevřete nebo extrahování souboru zip a přejděte do složky "Generic". V obecné složce, zobrazí se dva soubory, z nichž jeden je *VPNSettings.xml*.
3. Otevřít *VPNSettings.xml* v libovolném prohlížeči nebo editoru xml. V souboru xml vyhledejte následující pole:

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Pokud *ServerCertRotCn* a *ServerCertIssuerCn* jsou "DigiCert globální kořenové certifikační Autority", nejsou touto aktualizací ovlivněny a není nutné postupujte podle kroků v tomto článku. Pokud ale zobrazí se něco jiného, certifikát brány je v rámci aktualizace a se převedou.

### <a name="classic"></a>Classic

1. Na klientském počítači přejděte do umístění `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`. Ve složce ID brány můžete zobrazit certifikát.
2. Na kartě Obecné pro certifikát ověřte, že vystavující autorita "DigiCert globální kořenové certifikační Autority". Pokud máte nic jiného než vystavující autorita, váš certifikát brány je v rámci aktualizace a se převedou.

## <a name="2-check-certificate-transition-schedule"></a>2. Ověření plánu přechodu certifikátu

Pokud váš certifikát není v rámci aktualizace, váš certifikát brány se převedou. Odkazovat **důležité** Poznámka pro časů přechodů. Po aktualizaci P2S klienti nebudou moct připojit pomocí jejich starý profil. Musíte vygenerovat nový klient profily sítě VPN a nainstalovat na klientských počítačích.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Vytvořit profil konfigurace klienta VPN

Po certifikátu byla převedena, stáhněte si nový profil VPN (konfiguračních souborů klienta VPN) na webu Azure Portal. Pokyny najdete v tématu [vytvoření a instalace konfiguračních souborů klienta VPN](point-to-site-vpn-client-configuration-azure-cert.md). Nemusíte generovat nový klientské certifikáty.

## <a name="4-deploy-vpn-client-profile"></a>4. Nasadit profil klienta VPN

Nasaďte nový profil na všechny klienty VPN typu point-to-site. Klienti VPN dojde ke ztrátě připojení, dokud se nový profil sítě VPN pro připojení point-to-site je stažen a nasadí na klientská zařízení. Klientské certifikáty, které jsou již nainstalovány na počítače klienta VPN není nutné ho znovu vystavit.

## <a name="5-connect-the-vpn-client"></a>5. Připojení klienta VPN

Připojení k Azure z klienta VPN jako obvykle.