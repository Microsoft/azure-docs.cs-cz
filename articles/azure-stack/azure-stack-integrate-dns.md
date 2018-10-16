---
title: Integrace datových center Azure Stack – DNS
description: Zjistěte, jak integrovat Azure Stack DNS s vaším datovým centrem DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: bf412809f9d10296ad600e64abb6d870dbb88d3e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339674"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integrace datových center Azure Stack – DNS
Abyste mohli přístup ke koncovým bodům služby Azure Stack (**portál**, **adminportal**, **správu**, **adminmanagement**atd.)  z mimo Azure Stack budete muset integraci služeb Azure Stack DNS servery DNS, které jsou hostiteli zóny DNS, který chcete použít ve službě Azure Stack.

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS – obor názvů
Je nutné zadat některé důležité informace související s DNS při nasazení Azure Stack.


|Pole  |Popis  |Příklad:|
|---------|---------|---------|
|Oblast|Zeměpisné umístění vašeho nasazení Azure stacku.|`east`|
|Název externí domény|Název zóny, kterou chcete použít pro nasazení Azure Stack.|`cloud.fabrikam.com`|
|Internal Domain Name|Název interní zóny, který se používá pro služby infrastruktury ve službě Azure Stack.  Je integrovaná adresářová služba a privátní (nedostupný z mimo nasazení Azure Stack).|`azurestack.local`|
|Server DNS pro předávání|Servery DNS, které se používají k předávání dotazů DNS, zón a záznamů DNS, které jsou hostované mimo Azure Stack, buď na firemním intranetu nebo veřejným Internetem.|`10.57.175.34`<br>`8.8.8.8`|
|Předponu názvu (volitelné)|Chcete, aby vaší službě Azure Stack infrastrukturu role instance názvy počítačů mít předponu názvu.  Pokud se nezadá, výchozí hodnota je `azs`.|`azs`|

Plně kvalifikovaný název domény (FQDN) koncových bodů a nasazení Azure Stack je kombinací parametrů oblasti a parametr externí název domény. Pomocí hodnot z příkladů v předchozí tabulce, plně kvalifikovaný název domény pro toto nasazení Azure Stack by byl následující název:

`east.cloud.fabrikam.com`

V důsledku toho uvedené příklady některých z koncových bodů pro toto nasazení může vypadat třeba následující adresy URL:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

K používání oboru názvů tohoto příkladu DNS pro nasazení služby Azure Stack, je potřeba následující podmínky:

- Zóna `fabrikam.com` je registrovaný pomocí doménového registrátora, interní podnikový server DNS nebo pomocí obou v závislosti na vaše požadavky na název řešení.
- Podřízená doména `cloud.fabrikam.com` existuje v rámci zóny `fabrikam.com`.
- Servery DNS, které jsou hostiteli zóny `fabrikam.com` a `cloud.fabrikam.com` dosažitelný z nasazení služby Azure Stack.

Aby bylo možné přeložit názvy DNS pro koncové body služby Azure Stack a instance z externí služby Azure Stack, budete muset integraci servery DNS, které jsou hostiteli externí zóny DNS pro službu Azure Stack se servery DNS, které jsou hostiteli nadřazenou zónu, kterou chcete použít.


## <a name="resolution-and-delegation"></a>Překládání a delegování

Existují dva typy serverů DNS:

- Autoritativní server DNS hostí zóny DNS. Odpovídá pouze na dotazy DNS pro záznamy v těchto zónách.
- Rekurzivní server DNS nehostuje zóny DNS. Odpovídá na všechny dotazy DNS voláním autoritativních serverů DNS, které shromáždí potřebná data.

Azure Stack zahrnuje oba autoritativnímu a rekurzivních serverů DNS. Rekurzivních serverů slouží k překladu názvů všeho s výjimkou interní privátní zónu a externí veřejné zóny DNS pro toto nasazení Azure Stack. 

![Architektura služby Azure Stack DNS](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Překlad externích názvů DNS v Azure stacku

K překladu názvů DNS pro koncové body mimo Azure Stack (například: www.bing.com), budete muset zadat servery DNS, které služby Azure Stack slouží k předávání DNS požadavků, u kterých není autoritativní Azure Stack. Pro nasazení serverů DNS, které se předá požadavky na služby Azure Stack se vyžadují v listu nasazení (do pole pro předávání DNS). Zadejte alespoň dva servery v tomto poli pro odolnost proti chybám. Bez těchto hodnot nasazení Azure Stack se nezdaří.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurace podmíněné předávání DNS

> [!IMPORTANT]
> To platí pouze pro nasazení služby AD FS.

Chcete-li povolit překlad názvů s vaší současnou infrastrukturou DNS, nakonfigurujte podmíněné předávání.

Přidat podmíněné předávání, musíte použít privilegovaný koncový bod.

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s koncovým bodem privilegovaných ve službě Azure Stack.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními (Spustit jako správce) a připojit k IP adrese privileged koncového bodu. Použijte přihlašovací údaje pro ověřování CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Po připojení ke koncovému bodu privilegovaných, spusťte následující příkaz prostředí PowerShell. Nahraďte ukázkové hodnoty, opatřeného váš název domény a IP adresy serverů DNS, kterou chcete použít.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Překlad názvů Azure Stack DNS od mimo Azure Stack
Autoritativní servery jsou ty, které obsahují informace o zóně externí DNS a žádné uživatelem vytvořené zóny. Integrace s těmito servery umožňující delegování zóny nebo podmíněné předávání k překladu názvů Azure Stack DNS od mimo Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Získejte informace o externí koncový bod serveru DNS

Pokud chcete integrovat nasazení Azure Stack s vaší infrastrukturou DNS, budete potřebovat následující informace:

- Server DNS plně kvalifikovaných názvů domén
- IP adresy serveru DNS

Plně kvalifikované názvy domény pro servery Azure Stack DNS mít následující formát:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Použití ukázkové hodnoty, plně kvalifikovaných názvů domény DNS servery jsou:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Tyto informace se rovněž vytvoří na konci všechna nasazení Azure Stack v souboru s názvem `AzureStackStampDeploymentInfo.json`. Tento soubor je umístěn v `C:\CloudDeployment\logs` složky nasazení virtuálního počítače. Pokud si nejste jistí, jaké hodnoty byly použity pro vaše nasazení Azure Stack, můžete získat hodnoty z tohoto umístění.

Pokud nasazení virtuálního počítače už není k dispozici nebo je nedostupný, můžete získat hodnoty tím, že připojení ke koncovému bodu privilegovaných `Get-AzureStackInfo` rutiny Powershellu. Další informace najdete v tématu [privilegovaných koncový bod](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Nastavení podmíněné předávání do služby Azure Stack

Nejjednodušší a nejbezpečnější způsob integrace služby Azure Stack s infrastruktury služby DNS je provést podmíněné předávání zóny ze serveru, který hostuje nadřazenou zónu. Tento přístup se doporučuje, pokud máte přímou kontrolu nad servery DNS, které hostují nadřazené zóně oboru názvů služby Azure Stack externí DNS.

Pokud nejste obeznámeni s postup podmíněné předávání s DNS, najdete v následujícím článku na webu TechNet: [přiřazení pro název domény pro podmíněné předávání](https://technet.microsoft.com/library/cc794735), nebo dokumentaci k řešení DNS.

Ve scénářích, kde jste zadali externí Azure Stack zónu DNS, aby vypadala jako podřízenou doménu s názvem vaší firemní domény nelze použít podmíněné předávání. Delegování DNS musí být nakonfigurovaný.

Příklad:

- Název domény podnikový server DNS: `contoso.com`
- Název domény externího DNS Azure Stack: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegování externí zónu DNS do služby Azure Stack

Pro názvy DNS na jít přeložit z mimo nasazení služby Azure Stack budete muset nastavit delegování DNS.

Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. Na stránce správy DNS vašeho registrátora upravte záznamy NS a nahraďte je záznamy NS pro zónu NS ve službě Azure Stack.

Většina registrátorů DNS vyžaduje minimálně dva servery DNS delegování dokončíte.

## <a name="next-steps"></a>Další postup

[Integrace brány firewall](azure-stack-firewall.md)
