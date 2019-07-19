---
title: Porovnání směrných plánů s monitorováním integrity souborů v Azure Security Center | Microsoft Docs
description: Přečtěte si, jak porovnat směrné plány s monitorováním integrity souborů v Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: afc03baa71f17deb0b923f483fde214a86c5e9b4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296472"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porovnání směrných plánů pomocí monitorování integrity souborů (FIM)

Monitorování integrity souborů (FIM) vás informuje, když dojde ke změnám citlivých oblastí ve vašich prostředcích, takže můžete prozkoumat a vyřešit neoprávněné aktivity. FIM monitoruje soubory Windows, Registry Windows a soubory Linux.

Toto téma vysvětluje, jak povolit FIM pro soubory a Registry. Další informace o produktu FIM najdete v tématu [monitorování integrity souborů v Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Proč používat produkt FIM?

Operační systém, aplikace a přidružené konfigurace řídí chování a stav zabezpečení vašich prostředků. Proto útočníci cílí na soubory, které ovládají vaše prostředky, aby overtake operační systém prostředku a/nebo prováděly aktivity, aniž by se zjistily.

Mnoho standardů dodržování předpisů, jako je PCI-DSS & ISO 17799, ale vyžaduje implementaci ovládacích prvků FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Povolit integrované rekurzivní kontroly registru

Výchozí nastavení podregistru služby FIM nabízí pohodlný způsob, jak monitorovat rekurzivní změny v rámci běžných oblastí zabezpečení.  Nežádoucí osoba může například nakonfigurovat skript, který se spustí v kontextu LOCAL_SYSTEM, nakonfigurováním spuštění při spuštění nebo vypnutí.  Chcete-li monitorovat změny tohoto typu, povolte vestavěnou kontrolu.  

![Registru](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekurzivní kontroly se vztahují jenom na Doporučené podregistry zabezpečení a ne na vlastní cesty registru.  

## <a name="adding-a-custom-registry-check"></a>Přidání vlastní kontroly registru

Směrné plány FIM začínají určením vlastností známého stavu, který je pro operační systém a podporu aplikace v pořádku.  V tomto příkladu se zaměříme na konfigurace zásad hesel pro Windows Server 2008 a novější.


|Název zásady                 | Nastavení registru|
|---------------------------------------|-------------|
|Řadič domény: Odmítnout změny hesla účtu počítače| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Člen domény: Digitálně šifrovat nebo podepsat data zabezpečeného kanálu (vždy)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Člen domény: Digitálně šifrovat data zabezpečeného kanálu (Pokud je to možné)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Člen domény: Digitálně podepsat data zabezpečeného kanálu (Pokud je to možné)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Člen domény: Zakázat změny hesla účtu počítače|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Člen domény: Maximální stáří hesla účtu počítače|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Člen domény: Vyžadovat silný klíč relace (Windows 2000 nebo novější)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpečení sítě: Omezit protokol NTLM:  Ověřování protokolem NTLM v této doméně|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpečení sítě: Omezit protokol NTLM: Přidat výjimky serveru v této doméně|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpečení sítě: Omezit protokol NTLM: Auditovat ověřování NTLM v této doméně|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Další informace o nastaveních registru podporovaných různými verzemi operačních systémů najdete v [referenční tabulce nastavení zásady skupiny](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Konfigurace produktu FIM pro monitorování standardních hodnot registru:*

1. V okně **Přidat registr systému Windows pro Change Tracking** zadejte do textového pole **klíč registru systému Windows** klíč registru.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Povolení produktu FIM v registru](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Sledování změn souborů Windows

1. V okně **Přidat soubor Windows pro Change Tracking** zadejte do textového pole **zadat cestu** složku obsahující soubory, které chcete sledovat. V příkladu na následujícím obrázku se **Webová aplikace Contoso** nachází v D:\. jednotka ve struktuře složek **ContosWebApp**  
1. Vytvořte vlastní položku souboru systému Windows zadáním názvu třídy nastavení, povolením rekurze a zadáním horní složky se zástupnou příponou (*).

    ![Povolit FIM pro soubor](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Načítání dat změn

Data monitorování integrity souborů se nacházejí v sadě tabulek Azure Log Analytics/ConfigurationChange.  

 1. Nastavte časový rozsah pro načtení souhrnu změn podle prostředku.
V následujícím příkladu načítáme všechny změny za posledních 14 dní v kategoriích registru a souborů:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Chcete-li zobrazit podrobnosti o změnách v registru:

    1. Odebrat **soubory** z klauzule **WHERE** , 
    1. Odeberte řádek Shrnutí a nahraďte ho klauzulí řazení:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Sestavy můžete exportovat do sdíleného svazku clusteru pro účely archivace nebo kanálu do sestavy Power BI.  

![Data FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)