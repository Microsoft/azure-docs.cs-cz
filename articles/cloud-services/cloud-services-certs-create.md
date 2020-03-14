---
title: Cloud Services a certifikáty pro správu | Microsoft Docs
description: Naučte se vytvářet a používat certifikáty s Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 783343dd8877bdf18e783494960c3052c293cc7c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248135"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Přehled certifikátů pro Azure Cloud Services
Certifikáty se používají v Azure pro cloudové služby ([certifikáty služeb](#what-are-service-certificates)) a pro ověřování pomocí rozhraní API pro správu ([certifikáty pro správu](#what-are-management-certificates)). Toto téma obsahuje obecný přehled obou typů certifikátů, jak je [vytvořit](#create) a nasadit do Azure.

Certifikáty, které se používají v Azure, jsou certifikáty x. 509 v3 a můžou být podepsané jiným důvěryhodným certifikátem nebo můžou být podepsané svým držitelem. Certifikát podepsaný svým držitelem je podepsaný vlastním tvůrcem, proto není ve výchozím nastavení důvěryhodný. Většina prohlížečů může tento problém ignorovat. Certifikáty podepsané svým držitelem byste měli používat jenom při vývoji a testování vašich cloudových služeb. 

Certifikáty používané v Azure můžou obsahovat soukromý nebo veřejný klíč. Certifikáty mají kryptografický otisk, který poskytuje prostředky k jejich identifikaci jednoznačným způsobem. Tento kryptografický otisk se používá v [konfiguračním souboru](cloud-services-configure-ssl-certificate-portal.md) Azure k identifikaci certifikátu, který by měla cloudová služba používat. 

>[!Note]
>Azure Cloud Services nepřijímá zašifrovaný certifikát AES256-SHA256.

## <a name="what-are-service-certificates"></a>Co jsou certifikáty služby?
Certifikáty služeb jsou připojené ke cloudovým službám a umožňují zabezpečenou komunikaci do a ze služby. Pokud jste například nasadili webovou roli, měli byste dodat certifikát, který může ověřit vystavený koncový bod HTTPS. Certifikáty služby definované v definici služby se automaticky nasadí do virtuálního počítače, na kterém běží instance vaší role. 

Certifikáty služby můžete do Azure nahrát buď pomocí Azure Portal, nebo pomocí modelu nasazení Classic. Certifikáty služeb jsou přidružené ke konkrétní cloudové službě. Jsou přiřazeny k nasazení v definičním souboru služby.

Certifikáty služeb je možné spravovat odděleně od služeb a můžou je spravovat různí jednotlivci. Vývojář může například nahrát balíček služby, který odkazuje na certifikát, který správce IT předtím nahrál do Azure. Správce IT může tento certifikát spravovat a obnovovat (Změna konfigurace služby), aniž by musel nahrávat nový balíček služby. Aktualizace bez nového balíčku služby je možná, protože logický název, název úložiště a umístění certifikátu jsou v definičním souboru služby a i když je v konfiguračním souboru služby zadaný kryptografický otisk certifikátu. Chcete-li aktualizovat certifikát, je nutné odeslat nový certifikát a změnit hodnotu kryptografického otisku v konfiguračním souboru služby.

>[!Note]
>[Cloud Services nejčastější dotazy ke konfiguraci a správě](cloud-services-configuration-and-management-faq.md) obsahují některé užitečné informace o certifikátech.

## <a name="what-are-management-certificates"></a>Co jsou certifikáty pro správu?
Certifikáty pro správu umožňují ověřování pomocí modelu nasazení Classic. Mnoho programů a nástrojů (například sadu Visual Studio nebo Azure SDK) tyto certifikáty používá k automatizaci konfigurace a nasazení různých služeb Azure. Nevztahují se ke cloudovým službám. 

> [!WARNING]
> Dej si pozor! Tyto typy certifikátů umožňují všem, kteří se s nimi ověřují, spravovat předplatné, ke kterému jsou přidružené. 
> 
> 

### <a name="limitations"></a>Omezení
U každého předplatného je povolený limit 100 certifikátů pro správu. K dispozici je také limit 100 certifikátů pro správu pro všechna předplatná v rámci konkrétního ID uživatele správce služeb. Pokud ID uživatele pro správce účtu již bylo použito k přidání certifikátů pro správu 100 a vyžaduje více certifikátů, můžete přidat spolusprávce a přidat další certifikáty. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Vytvoření nového certifikátu podepsaného svým držitelem
Můžete použít libovolný nástroj dostupný k vytvoření certifikátu podepsaného svým držitelem, pokud dodržují tato nastavení:

* Certifikát X. 509.
* Obsahuje privátní klíč.
* Vytvořeno pro výměnu klíčů (soubor. pfx).
* Název subjektu se musí shodovat s doménou používanou pro přístup ke cloudové službě.

    > Nemůžete získat certifikát SSL pro cloudapp.net (nebo pro žádnou doménu související s Azure); název subjektu certifikátu se musí shodovat s názvem vlastní domény použitým pro přístup k vaší aplikaci. Například **contoso.NET**, ne **contoso.cloudapp.NET**.

* Minimálně 2048 bitů šifrování.
* **Pouze certifikát služby**: certifikát na straně klienta se musí nacházet v *osobním* úložišti certifikátů.

Existují dva jednoduché způsoby, jak vytvořit certifikát ve Windows, pomocí nástroje `makecert.exe` nebo služby IIS.

### <a name="makecertexe"></a>Makecert.exe
Tento nástroj je zastaralý a již není dokumentován. Další informace najdete v [tomto článku na webu MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Pokud chcete použít certifikát s IP adresou a nikoli doménou, použijte IP adresu v parametru-DnsName.


Pokud chcete tento [certifikát použít s portálem pro správu](../azure-api-management-certs.md), exportujte ho do souboru **. cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internetová informační služba (IIS)
Existuje mnoho stránek na internetu, které pokrývají, jak to provést se službou IIS. [Tady](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) je skvělé, který jsme našli, že je to v podstatě. 

### <a name="linux"></a>Linux
[Tento](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článek popisuje, jak vytvořit certifikáty pomocí SSH.

## <a name="next-steps"></a>Další kroky
[Nahrajte do Azure Portal certifikát služby](cloud-services-configure-ssl-certificate-portal.md).

Nahrajte do Azure Portal [certifikát rozhraní API pro správu](../azure-api-management-certs.md) .




