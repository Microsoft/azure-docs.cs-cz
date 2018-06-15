---
title: Zásobník datacenter integrace se službou Azure - DNS
description: Zjistěte, jak integrovat Azure zásobníku DNS s vaším datovým centrem DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 5bdac2f3e6082f9449800fe2d4b303e2d59ade46
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29733855"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Zásobník datacenter integrace se službou Azure - DNS
Pro přístup k koncové body Azure zásobníku (`portal`, `adminportal`, `management`, `adminmanagement`atd.)  ze zásobníku mimo Azure budete muset integraci služby Azure DNS zásobníku se servery DNS, které jsou hostiteli zóny DNS, kterou chcete použít v zásobníku Azure.

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS namespace
Je nutné zajistit některé důležité informace související s DNS při nasazení Azure zásobníku.


|Pole  |Popis  |Příklad:|
|---------|---------|---------|
|Oblast|Geografické umístění nasazení Azure zásobníku.|`east`|
|External Domain Name|Název zóny, který chcete použít pro vaše nasazení Azure zásobníku.|`cloud.fabrikam.com`|
|Internal Domain Name|Název interní zóny, který se používá pro služby infrastruktury v zásobníku Azure.  Je integrované adresářové služby a privátní (nedostupný z mimo Azure zásobníku nasazení).|`azurestack.local`|
|Server DNS pro předávání|Servery DNS, které se používají k předávání dotazů DNS, zóny DNS a záznamy, které jsou hostované mimo Azure zásobníku, buď v podnikovém intranetu nebo veřejného Internetu.|`10.57.175.34`<br>`8.8.8.8`|
|Pojmenování předpony (volitelné)|Chcete Azure zásobníku infrastruktury role instance počítače názvy tak, aby měl předpony názvu.  Pokud není zadaná, výchozí hodnota je `azs`.|`azs`|

Plně kvalifikovaný název domény (FQDN) nasazení Azure zásobníku a koncových bodů je kombinace parametr oblasti a parametr externí název domény. Použijte hodnoty z příkladů v předchozí tabulce, bude plně kvalifikovaný název domény pro toto nasazení Azure zásobníku následující název:

`east.cloud.fabrikam.com`

Uvedené příklady některých koncových bodů pro toto nasazení jako takový bude vypadat následující adresy URL:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Pokud chcete použít tento příklad názvů DNS pro nasazení služby Azure zásobníku, jsou požadované následující podmínky:

- Zóna `fabrikam.com` je zaregistrován pomocí doménového registrátora, interní podnikový server DNS nebo obojí, v závislosti na vaše požadavky na překlad názvů.
- Podřízená doména `cloud.fabrikam.com` existuje v rámci zóny `fabrikam.com`.
- Servery DNS, které jsou hostiteli zóny `fabrikam.com` a `cloud.fabrikam.com` lze přejít z nasazení Azure zásobníku.

Abyste mohli k překladu názvů DNS pro koncové body Azure zásobníku a instancí z zásobníku mimo Azure, musíte integrovat servery DNS, které hostitel externí zóny DNS pro Azure zásobníku se servery DNS, které jsou hostiteli nadřazenou zónu, kterou chcete použít.


## <a name="resolution-and-delegation"></a>Překládání a delegování

Existují dva typy serverů DNS:

- Autoritativní server DNS hostuje zóny DNS. Odpovídá pouze na dotazy DNS pro záznamy v těchto zónách.
- Rekurzivní server DNS nehostuje zóny DNS. Odpovídá na všechny dotazy DNS voláním autoritativních serverů DNS, které shromáždí potřebná data.

Azure Stack zahrnuje obě autoritativní a servery DNS rekurzivní. Rekurzivní servery slouží k přeložení názvy všechno kromě interní privátní zóny a externí veřejné zónu DNS pro toto nasazení Azure zásobníku. 

![Architektura Azure DNS zásobníku](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Řešení externí názvy DNS z Azure zásobníku

K překladu názvů DNS pro koncové body mimo Azure zásobníku (například: www.bing.com), je třeba zadat servery DNS, které můžete použít Azure zásobníku přesměrovat žádosti DNS, pro které není autoritativní zásobník Azure. Pro nasazení serverů DNS, které Azure zásobníku předá požadavky jsou potřeba v listu nasazení (do pole pro předávání DNS). Zadejte alespoň dva servery v tomto poli pro odolnost proti chybám. Bez těchto hodnot zásobník Azure nasazení se nezdaří.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurace podmíněného předávání DNS

> [!IMPORTANT]
> To platí jenom pro nasazení služby AD FS.

Chcete-li povolit překlad s existující infrastruktury služby DNS, nakonfigurujte podmíněné předávání.

Přidání serveru pro podmíněné předávání, musíte použít privilegovaný koncový bod.

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s privilegované koncového bodu v zásobníku Azure.

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními (Spustit jako správce) a připojte na IP adresu privilegované koncového bodu. Použití pověření pro ověřování CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Po připojení ke koncovému bodu privilegované, spusťte následující příkaz prostředí PowerShell. Nahraďte ukázkové hodnoty součástí názvu domény a IP adresy serverů DNS, kterou chcete použít.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Překladu názvů DNS zásobník Azure z mimo Azure zásobníku
Autoritativní servery jsou ty, které obsahují informace o zóně externí DNS a žádné zóny vytvořené uživatelem. Integrate s tyto servery se dá povolit delegování zóny nebo podmíněné předávání k překladu názvů DNS zásobník Azure z mimo Azure zásobníku.

## <a name="get-dns-server-external-endpoint-information"></a>Získat informace o externí koncový bod serveru DNS

Chcete-li integrovat Azure zásobníku nasazení infrastruktury služby DNS, je třeba následující informace:

- Server DNS plně kvalifikovaných názvů domén
- DNS server IP addresses

Plně kvalifikované názvy domény pro servery Azure DNS zásobníku mít následující formát:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Použití ukázkové hodnoty, plně kvalifikovaných názvů domén v DNS servery jsou:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Tyto informace se také vytvoří na konci všechna nasazení Azure zásobníku v souboru s názvem `AzureStackStampDeploymentInfo.json`. Tento soubor je umístěný v `C:\CloudDeployment\logs` složky nasazení virtuálního počítače. Pokud si nejste jisti, jaké hodnoty měla použít pro vaše nasazení zásobník Azure, můžete získat hodnoty z tohoto umístění.

Pokud nasazení virtuálního počítače již není k dispozici nebo není dostupný, můžete získat hodnoty připojení ke koncovému bodu privilegovaných a spuštěním `Get-AzureStackInfo` rutiny prostředí PowerShell. Další informace o privileged koncový bod najdete v článku (vložení propojit článek sem).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Nastavení podmíněné předávání do Azure zásobníku

Nejjednodušší a nejbezpečnější způsob, jak integrovat Azure zásobníku infrastruktury služby DNS je provést podmíněné předávání zóny ze serveru, který hostuje nadřazenou zónu. Tento postup se doporučuje, když máte přímou kontrolu nad servery DNS tohoto hostitele je zóna nadřazené oboru názvů zásobník Azure externí DNS.

Pokud si nejste obeznámeni s postup podmíněné předávání s DNS, najdete v následujícím článku na webu TechNet: [přiřadit pro podmíněné předávání pro název domény](https://technet.microsoft.com/library/cc794735), nebo dokumentaci týkající se řešení DNS.

Ve scénářích, kde jste zadali externí zónu DNS zásobník Azure, aby vypadala jako podřízenou doménu s názvem vaší podnikové doméně nelze použít podmíněné předávání. Delegování DNS musí být nakonfigurovaná.

Příklad:

- Název domény podnikový server DNS: `contoso.com`
- Název domény DNS externí Azure zásobníku: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegování externí zóny DNS se zásobníkem Azure

Názvy DNS jít přeložit z mimo nasazení služby Azure zásobníku musíte nastavit delegování DNS.

Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte v zásobníku Azure záznamy NS pro zónu.

Většina registrátorů DNS musíte poskytnout minimálně dva servery DNS pro delegování dokončíte.

## <a name="next-steps"></a>Další postup

[Integrace brány firewall](azure-stack-firewall.md)
