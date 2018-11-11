---
title: Cloud Services a správa certifikátů | Dokumentace Microsoftu
description: Další informace o vytváření a používání certifikátů s Microsoft Azure
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 9cf37e611dce5705a4c866f25afa59e5c1602ec4
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282199"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Přehled certifikátů pro Azure Cloud Services
Certifikáty se používají v Azure pro cloudové služby ([služby certifikátů](#what-are-service-certificates)) a pro ověřování pomocí rozhraní API pro správu ([certifikáty pro správu](#what-are-management-certificates)). Toto téma obsahuje obecný přehled o oba typy certifikátů, jak k [vytvořit](#create) a [nasazení](#deploy) do Azure.

Certifikáty používané v Azure jsou x.509 v3 certifikáty a může být podepsány jiný certifikát pro důvěryhodného nebo mohou být podepsaný svým držitelem. Certifikát podepsaný svým držitelem je podepsaný vlastním creator, proto není důvěryhodný ve výchozím nastavení. Většina prohlížečů můžete ignorovat tento problém. Byste měli používat jenom certifikáty podepsané svým držitelem při vývoji a testování vašich cloudových služeb. 

Certifikáty používané službou Azure může obsahovat privátního nebo veřejného klíče. Certifikáty mít kryptografický otisk, který poskytuje prostředky k identifikaci jednoznačným způsobem. Tímto kryptografickým otiskem se používá ve službě Azure [konfigurační soubor](cloud-services-configure-ssl-certificate-portal.md) k identifikaci který certifikát je Cloudová služba by měla používat. 

>[!Note]
>Azure Cloud Services nepřijímá zašifrovaný certifikát AES256 SHA256.

## <a name="what-are-service-certificates"></a>Co jsou certifikáty služeb?
Certifikáty služby jsou připojeny k cloudových služeb a povolení zabezpečené komunikace do a ze služby. Pokud jste nasadili webovou roli, by například chcete zadat certifikát, který může ověřit koncový bod HTTPS vystavené. Certifikáty služby, definované v definici služby se automaticky nasadí do virtuálního počítače, na kterém běží instance role. 

Certifikáty služby můžete nahrát do Azure, buď pomocí webu Azure portal nebo pomocí modelu nasazení classic. Certifikáty služby jsou spojeny s konkrétní cloudové služby. Jsou přiřazeny k nasazení v definičním souboru služby.

Certifikáty služeb se dají spravovat nezávisle z vašich služeb a může být spravován různí jednotlivci. Například vývojář může nahrát balíček služby, který odkazuje na certifikát, který správce IT má byl dříve odeslán do Azure. Správce IT můžete spravovat a obnovit tento certifikát (a změníte konfiguraci této služby) bez nutnosti k nahrání nového balíčku služby. Aktualizace bez nového balíčku služby je možné, protože logický název, název úložiště a umístění certifikátu naleznete v souboru definice služby a kryptografický otisk certifikátu je zadané v konfiguračním souboru služby. Pokud chcete aktualizovat certifikát, je pouze potřeba nahrát nový certifikát a změňte hodnotu kryptografického otisku v konfiguračním souboru služby.

>[!Note]
>[Cloud Services – nejčastější dotazy](cloud-services-faq.md) článek má některé užitečné informace o certifikátech.

## <a name="what-are-management-certificates"></a>Co jsou certifikáty pro správu?
Certifikáty pro správu umožňují ověření pomocí modelu nasazení classic. Mnoho programech a nástrojích (např. Visual Studio nebo pomocí sady SDK Azure) použijte tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. Tyto nemá relaci ve skutečnosti ke cloudovým službám. 

> [!WARNING]
> Dej si pozor! Tyto typy certifikátů povolit všem uživatelům, který se ověřuje pomocí nich ke správě předplatného, které jsou přidružené. 
> 
> 

### <a name="limitations"></a>Omezení
Platí limit 100 certifikátů pro správu na jedno předplatné. Také platí limit 100 certifikátů pro správu pro všechna předplatná zařazená pod ID správce konkrétní služby uživatele. Pokud ID uživatele pro účet správce již byl použit pro přidání 100 certifikáty pro správu a je potřeba pro další certifikáty, můžete přidat spolupracující správce přidat další certifikáty. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Vytvořit nový certifikát podepsaný svým držitelem
Můžete použít jakýkoli nástroj k vytvoření certifikátu podepsaného svým držitelem, za předpokladu, že dodržovat tato nastavení:

* Certifikát X.509.
* Obsahuje privátní klíč.
* Vytvořen pro výměnu klíčů (soubor .pfx).
* Název subjektu musí odpovídat doménu, kterou používá pro přístup ke cloudové službě.

    > Nelze získat certifikát SSL pro cloudapp.net (nebo pro všechny související s Azure) domény. Název subjektu certifikátu musí odpovídat názvu vlastní domény pro přístup k aplikaci. Například **contoso.net**, nikoli **contoso.cloudapp.net**.

* Minimálně 2048bitové šifrování.
* **Služba certifikátu pouze**: certifikát na straně klienta se musí nacházet v *osobní* úložiště certifikátů.

Existují dva snadných způsobů, jak vytvořit certifikát na Windows, se `makecert.exe` nástroj nebo službu IIS.

### <a name="makecertexe"></a>Makecert.exe
Tento nástroj je zastaralý a už jsou zde uvedeny. Další informace najdete v tématu [článku na webu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Pokud chcete použít certifikát s IP adresou namísto domény, použijte parametr - DnsName IP adresu.


Pokud chcete použít toto [certifikátu pomocí portálu pro správu](../azure-api-management-certs.md), exportujte ho do **.cer** souboru:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internetová informační služba (IIS)
Existuje mnoho stránek na Internetu, které se dozvíte, jak to udělat pomocí služby IIS. [Tady](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) je skvělé se mi najít myslím vysvětluje dobře. 

### <a name="linux"></a>Linux
[To](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článek popisuje postup vytvoření certifikátů pomocí protokolu SSH.

## <a name="next-steps"></a>Další postup
[Nahrát svůj certifikát služby na webu Azure portal](cloud-services-configure-ssl-certificate-portal.md).

Nahrát [certifikátu rozhraní API pro správu](../azure-api-management-certs.md) k webu Azure portal.

