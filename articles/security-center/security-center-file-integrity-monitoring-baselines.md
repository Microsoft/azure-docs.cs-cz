---
title: Monitorování integrity souborů ve službě Azure Security Center
description: Přečtěte si, jak porovnat směrné plány s monitorováním integrity souborů v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634688"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porovnání standardních hodnot s využitím monitorování integrity souborů

Monitorování integrity souborů (FIM) vás informuje, když dojde ke změnám citlivých oblastí ve vašich prostředcích, takže můžete prozkoumat a vyřešit neoprávněné aktivity. FIM monitoruje soubory Windows, Registry Windows a soubory Linux.

Toto téma vysvětluje, jak povolit FIM pro soubory a Registry. Další informace o produktu FIM najdete v tématu [monitorování integrity souborů v Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Proč používat produkt FIM?

Operační systém, aplikace a přidružené konfigurace řídí chování a stav zabezpečení vašich prostředků. Proto útočníci cílí na soubory, které ovládají vaše prostředky, aby overtake operační systém prostředku a/nebo prováděly aktivity, aniž by se zjistily.

Mnoho standardů dodržování předpisů, jako je PCI-DSS & ISO 17799, ale vyžaduje implementaci ovládacích prvků FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Povolit integrované rekurzivní kontroly registru

Výchozí nastavení podregistru služby FIM nabízí pohodlný způsob, jak monitorovat rekurzivní změny v rámci běžných oblastí zabezpečení.  Nežádoucí osoba může například nakonfigurovat skript, který se spustí v kontextu LOCAL_SYSTEM, nakonfigurováním spuštění při spuštění nebo vypnutí.  Chcete-li monitorovat změny tohoto typu, povolte vestavěnou kontrolu.  

![Registr](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekurzivní kontroly se vztahují jenom na Doporučené podregistry zabezpečení a ne na vlastní cesty registru.  

## <a name="add-a-custom-registry-check"></a>Přidat vlastní kontrolu registru

Směrné plány FIM začínají určením vlastností známého stavu, který je pro operační systém a podporu aplikace v pořádku.  V tomto příkladu se zaměříme na konfigurace zásad hesel pro Windows Server 2008 a novější.


|Název zásady                 | Nastavení registru|
|---------------------------------------|-------------|
|Řadič domény: Nepovolit změnu hesla účtu počítače| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Člen domény: Vždy digitálně zašifrovat nebo podepsat data zabezpečeného kanálu|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Člen domény: Je-li možno, digitálně zašifrovat data zabezpečeného kanálu|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Člen domény: Je-li možno, digitálně podepsat data zabezpečeného kanálu|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Člen domény: Nepovolit změnu hesla účtu počítače|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Člen domény: Maximální doba platnosti hesla účtu počítače|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Člen domény: Vyžadovat silný klíč relace (Windows 2000 nebo vyšší)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpečení sítě: omezit protokol NTLM: ověřování NTLM v této doméně|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpečení sítě: Omezit protokol NTLM: Přidat výjimky pro servery v této doméně|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpečení sítě: omezit protokol NTLM: Auditovat ověřování NTLM v této doméně|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Další informace o nastaveních registru podporovaných různými verzemi operačních systémů najdete v [referenční tabulce nastavení zásady skupiny](https://www.microsoft.com/download/confirmation.aspx?id=25250).

Konfigurace produktu FIM pro monitorování standardních hodnot registru:

1. V okně **Přidat registr Windows pro Change Tracking** zadejte do textového pole **klíč registru Windows** následující klíč registru:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="Povolení produktu FIM v registru":::

## <a name="track-changes-to-windows-files"></a>Sledování změn souborů Windows

1. V okně **Přidat soubor Windows pro Change Tracking** zadejte do textového pole **zadat cestu** složku obsahující soubory, které chcete sledovat. V příkladu na následujícím obrázku se **Webová aplikace Contoso** nachází v D:\. jednotka ve struktuře složek **ContosWebApp**  
1. Vytvořte vlastní položku souboru systému Windows zadáním názvu třídy nastavení, povolením rekurze a zadáním horní složky se zástupnou příponou (*).

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="Povolit FIM pro soubor":::

## <a name="retrieve-change-data"></a>Načíst data změny

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