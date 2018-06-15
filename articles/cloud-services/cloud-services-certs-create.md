---
title: Cloudové služby a certifikáty pro správu | Microsoft Docs
description: Zjistěte, jak vytvořit a použít certifikáty s Microsoft Azure
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 6a1e4f5316cc0321c1409f9e48daeae6ee483bf6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
ms.locfileid: "29969198"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Přehled certifikáty pro cloudové služby Azure
Certifikáty se používají v Azure pro cloudové služby ([služby certifikáty](#what-are-service-certificates)) a pro ověřování pomocí rozhraní API pro správu ([certifikáty pro správu](#what-are-management-certificates)). Toto téma obsahuje obecný přehled oba typy certifikátů, jak k [vytvořit](#create) a [nasazení](#deploy) je do Azure.

Certifikáty používané v Azure jsou x.509 v3 certifikáty a může být podepsány jiný certifikát důvěryhodné nebo mohou být podepsaný svým držitelem. Certifikát podepsaný svým držitelem je podepsána vlastní creator, proto není důvěryhodný ve výchozím nastavení. Tento problém můžete ignorovat většina prohlížečů. Byste měli používat jenom certifikáty podepsané svým držitelem při vývoji a testování vaší cloudové služby. 

Certifikáty používané Azure může obsahovat privátního nebo veřejného klíče. Certifikáty mít kryptografický otisk, která poskytuje prostředky k identifikaci je jednoznačným způsobem. Tímto kryptografickým otiskem slouží ve službě Azure [konfigurační soubor](cloud-services-configure-ssl-certificate-portal.md) k identifikaci měli použít který certifikátu cloudové služby. 

## <a name="what-are-service-certificates"></a>Jaké jsou certifikáty služby?
Certifikáty služby jsou připojené ke cloudové služby a zajištění zabezpečené komunikace do a ze služby. Pokud jste nasadili webovou roli, by například chcete zadat certifikát, který může ověřit zveřejněné koncový bod HTTPS. Certifikáty služby definované v definice služby, se automaticky nasadí do virtuálního počítače, který je spuštěna instance role. 

Certifikáty služby můžete nahrát do Azure, buď pomocí portálu Azure nebo pomocí modelu nasazení classic. Certifikáty služby jsou přidruženy ke konkrétní cloudové službě. Jsou přiřazeny k nasazení v definičním souboru služby.

Certifikáty služby může být spravován od něj odděleně vašim službám a mohou být spravovány nástrojem různí jednotlivci. Vývojář může například odeslat balíček služby, který odkazuje na certifikát, který se správce IT se předtím nahrála do Azure. Správce IT můžete spravovat a obnovit tento certifikát (Změna konfigurace služby) bez nutnosti nahrát nový balíček pro službu. Aktualizace bez nový balíček služby je možné, protože logický název, název úložiště a umístění certifikátu naleznete v souboru definice služby, a když kryptografický otisk certifikátu je uveden v konfiguračním souboru služby. Pokud chcete aktualizovat certifikát, je pouze potřeba nahrát nový certifikát a změňte hodnotu kryptografický otisk v konfiguračním souboru služby.

>[!Note]
>[Nejčastější dotazy týkající se služby Cloud](cloud-services-faq.md) článek obsahuje některé užitečné informace o certifikátech.

## <a name="what-are-management-certificates"></a>Jaké jsou certifikáty pro správu?
Certifikáty pro správu umožňují ověření pomocí modelu nasazení classic. Mnoho programy a nástroje (například Visual Studio nebo sadu Azure SDK) použít tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. Tyto spolu nesouvisejí skutečně cloudových služeb. 

> [!WARNING]
> Buďte opatrní! Tyto typy certifikátů, povolí všem uživatelům, kteří se mají spravovat předplatné, které jsou přidruženy ověřuje. 
> 
> 

### <a name="limitations"></a>Omezení
Existuje limit 100 certifikáty pro správu podle předplatného. Je také limit 100 certifikáty pro správu pro všechna předplatná pod ID specifické služby správce uživatele. Pokud ID uživatele pro účet správce již byl použit pro přidání 100 certifikáty pro správu a je nezbytné pro další certifikáty, můžete přidat společné správce o přidání dalších certifikátů. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Vytvořit nový certifikát podepsaný svým držitelem
Můžete použít jakýkoli nástroj, který je možné vytvořit certifikát podepsaný svým držitelem tak dlouho, dokud budou splňovat tato nastavení:

* Certifikát X.509.
* Obsahuje privátní klíč.
* Vytvoří pro výměnu klíčů (soubor .pfx).
* Název subjektu musí odpovídat domény používá pro přístup ke cloudové službě.

    > Nelze získat certifikát protokolu SSL pro cloudapp.net (nebo pro jakékoli souvisejících s Azure) domény; Název subjektu certifikátu musí odpovídat názvu vlastní domény, používá pro přístup k aplikaci. Například **contoso.net**, nikoli **contoso.cloudapp.net**.

* Minimálně 2048bitové šifrování.
* **Služba certifikátu pouze**: klientský certifikát se musí nacházet v *osobní* úložiště certifikátů.

Existují dva způsoby snadné vytvoření certifikátu v systému Windows, pomocí `makecert.exe` nástroje nebo služby IIS.

### <a name="makecertexe"></a>Makecert.exe
Tento nástroj je zastaralá a již jsou zde uvedeny. Další informace najdete v tématu [tohoto článku na webu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Pokud chcete použít certifikát s IP adresou místo k doméně, použijte parametr - DnsName IP adresu.


Pokud chcete použít [certifikátu pomocí portálu pro správu](../azure-api-management-certs.md), exportovat je do **.cer** souboru:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internetová informační služba (IIS)
Existuje mnoho stránek na Internetu, které se týkají jak to provést pomocí služby IIS. [Zde](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) je skvělým našel jsem myslím ji a vysvětluje. 

### <a name="linux"></a>Linux
[To](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článek popisuje, jak umožnit vytváření certifikátů pomocí protokolu SSH.

## <a name="next-steps"></a>Další postup
[Nahrajte certifikát služby do portálu Azure](cloud-services-configure-ssl-certificate-portal.md).

Nahrát [certifikát správy rozhraní API](../azure-api-management-certs.md) na portál Azure.

