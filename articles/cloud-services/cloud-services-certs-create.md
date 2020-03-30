---
title: Cloudové služby a certifikáty správy | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvářet a používat certifikáty v Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 783343dd8877bdf18e783494960c3052c293cc7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248135"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Přehled certifikátů pro Azure Cloud Services
Certifikáty se používají v Azure pro cloudové služby[(certifikáty služeb)](#what-are-service-certificates)a pro ověřování pomocí rozhraní API pro správu[(certifikáty pro správu).](#what-are-management-certificates) Toto téma poskytuje obecný přehled o obou typech certifikátů, jak je [vytvořit](#create) a nasadit do Azure.

Certifikáty používané v Azure jsou certifikáty x.509 v3 a mohou být podepsány jiným důvěryhodným certifikátem nebo mohou být podepsané svým držitelem. Certifikát podepsaný svým držitelem je podepsán vlastním tvůrcem, proto není ve výchozím nastavení důvěryhodný. Většina prohlížečů tento problém ignoruje. Certifikáty podepsané svým držitelem byste měli používat jenom při vývoji a testování cloudových služeb. 

Certifikáty používané Azure může obsahovat soukromý nebo veřejný klíč. Certifikáty mají kryptografický otisk, který poskytuje prostředky k jejich jednoznačné identifikaci. Tento kryptografický otisk se používá v [konfiguračním souboru](cloud-services-configure-ssl-certificate-portal.md) Azure k identifikaci certifikátu, který by měla cloudová služba používat. 

>[!Note]
>Azure Cloud Services nepřijímá šifrovaný certifikát AES256-SHA256.

## <a name="what-are-service-certificates"></a>Co jsou certifikáty služeb?
Certifikáty služeb jsou připojeny ke cloudovým službám a umožňují bezpečnou příchozí i odchozí komunikaci se službou. Například pokud jste nasadili webovou roli, měli byste zadat certifikát, který může ověřit exponovaný koncový bod HTTPS. Certifikáty služeb definované v definici služby se automaticky nasazují do virtuálního počítače, ve kterých je spuštěna instance vaší role. 

Certifikáty služeb můžete do Azure nahrát pomocí webu Azure Portal nebo pomocí modelu nasazení Classic. Certifikáty služeb jsou přidruženy ke konkrétní cloudové službě. Jsou přiřazeny k nasazení v definičním souboru služby.

Certifikáty služeb lze spravovat odděleně od vašich služeb a mohou být spravovány různými jednotlivci. Vývojář může například nahrát balíček služeb, který odkazuje na certifikát, který správce IT dříve odeslal do Azure. Správce IT může tento certifikát spravovat a obnovovat (změnou konfigurace služby), aniž by musel nahrávat nový balíček služby. Aktualizace bez nového balíčku služeb je možná, protože logický název, název úložiště a umístění certifikátu je v souboru definice služby a zatímco kryptografický otisk certifikátu je určen v konfiguračním souboru služby. Pokud chcete aktualizovat certifikát, stačí nahrát nový certifikát a změnit hodnotu kryptografického otisku v konfiguračním souboru služby.

>[!Note]
>Článek [Nejčastější dotazy ke službě Cloud Services – konfigurace a správa](cloud-services-configuration-and-management-faq.md) obsahuje některé užitečné informace o certifikátech.

## <a name="what-are-management-certificates"></a>Co jsou certifikáty pro správu?
Certifikáty pro správu vám umožňují ověřit se pomocí modelu nasazení Classic. Mnoho programů a nástrojů (například Visual Studio nebo Azure SDK) používá tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. Ty ve skutečnosti nesouvisí s cloudovými službami. 

> [!WARNING]
> Dej si pozor! Tyto typy certifikátů umožňují každému, kdo se s nimi ověří, spravovat předplatné, ke kterému je přidružen. 
> 
> 

### <a name="limitations"></a>Omezení
Na jedno předplatné je limit 100 certifikátů správy. K dispozici je také limit 100 certifikátů správy pro všechna předplatná v rámci konkrétního ID uživatele správce služby. Pokud již bylo id uživatele správce účtu použito k přidání 100 certifikátů správy a je potřeba další certifikáty, můžete přidat spolusprávce a přidat další certifikáty. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Vytvoření nového certifikátu podepsaného svým držitelem
K vytvoření certifikátu podepsaného svým držitelem můžete použít libovolný nástroj, pokud dodržuje tato nastavení:

* Certifikát X.509.
* Obsahuje soukromý klíč.
* Vytvořeno pro výměnu klíčů (soubor.pfx).
* Název subjektu se musí shodovat s doménou používanou pro přístup ke cloudové službě.

    > Nelze získat certifikát SSL pro cloudapp.net (nebo pro všechny azure související) domény; Název předmětu certifikátu se musí shodovat s vlastním názvem domény používaným pro přístup k aplikaci. Například **contoso.net**, nikoli **contoso.cloudapp.net**.

* Minimálně 2048bitové šifrování.
* **Pouze certifikát služby**: Certifikát na straně klienta musí být umístěn v úložišti *osobních* certifikátů.

Existují dva jednoduché způsoby vytvoření certifikátu `makecert.exe` v systému Windows pomocí nástroje nebo služby IIS.

### <a name="makecertexe"></a>Makecert.exe
Tento nástroj byl zastarala a již není dokumentován zde. Další informace naleznete v [tomto článku msdn](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Pokud chcete použít certifikát s IP adresou místo domény, použijte IP adresu v parametru -DnsName.


Chcete-li tento certifikát použít [s portálem pro správu](../azure-api-management-certs.md), exportujte jej do souboru **CER:**

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Existuje mnoho stránek na internetu, které pokrývají, jak to udělat se službě IIS. [Zde](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) je skvělý jsem zjistil, že myslím, že vysvětluje to dobře. 

### <a name="linux"></a>Linux
[Tento](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) článek popisuje, jak vytvořit certifikáty s SSH.

## <a name="next-steps"></a>Další kroky
[Nahrajte certifikát služby na portál Azure](cloud-services-configure-ssl-certificate-portal.md).

Nahrajte [certifikát rozhraní API pro správu](../azure-api-management-certs.md) na portál Azure.




