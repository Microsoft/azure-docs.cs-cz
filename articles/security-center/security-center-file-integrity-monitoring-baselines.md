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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664415"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porovnání standardních hodnot s využitím monitorování integrity souborů

Monitorování integrity souborů (FIM) vás informuje o tom, kdy dojde ke změnám v citlivých oblastech ve vašich prostředcích, takže můžete prozkoumat a řešit neautorizovanou aktivitu. FIM monitoruje soubory systému Windows, registry systému Windows a soubory Linux.

Toto téma vysvětluje, jak povolit FIM v souborech a registrech. Další informace o FIM najdete [v tématu Monitorování integrity souborů v Centru zabezpečení Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Proč používat FIM?

Operační systém, aplikace a přidružené konfigurace řídí chování a stav zabezpečení prostředků. Útočníci proto cílí na soubory, které řídí vaše prostředky, aby předběhli operační systém prostředku nebo provedli aktivity bez zjištění.

Ve skutečnosti mnoho norem pro dodržování předpisů, jako je PCI-DSS & ISO 17799, vyžaduje implementaci kontrol FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Povolení předdefinovaných rekurzivních kontrol registru

Výchozí hodnoty podregistru FIM poskytují pohodlný způsob sledování rekurzivních změn v rámci společných oblastí zabezpečení.  Protivník může například nakonfigurovat skript, který se má spustit v LOCAL_SYSTEM kontextu konfigurací spuštění při spuštění nebo vypnutí.  Chcete-li sledovat změny tohoto typu, povolte předdefinované vrácení se změnami.  

![Registr](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekurzivní kontroly se vztahují pouze na doporučené podregistry zabezpečení a nikoli na vlastní cesty registru.  

## <a name="adding-a-custom-registry-check"></a>Přidání vlastní kontroly registru

Směrné plány FIM začínají identifikací charakteristik stavu považovaného za dobrý pro operační systém a podpůrnou aplikací.  V tomto příkladu se zaměříme na konfigurace zásad hesel pro systém Windows Server 2008 a vyšší.


|Název zásady                 | Nastavení registru|
|---------------------------------------|-------------|
|Řadič domény: Nepovolit změnu hesla účtu počítače| STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Člen domény: Vždy digitálně zašifrovat nebo podepsat data zabezpečeného kanálu|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Člen domény: Je-li možno, digitálně zašifrovat data zabezpečeného kanálu|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Člen domény: Je-li možno, digitálně podepsat data zabezpečeného kanálu|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Člen domény: Nepovolit změnu hesla účtu počítače|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Člen domény: Maximální doba platnosti hesla účtu počítače|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Člen domény: Vyžadovat silný klíč relace (Windows 2000 nebo vyšší)|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpečení sítě: Omezit ntlm: Ověřování NTLM v této doméně|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpečení sítě: Omezit protokol NTLM: Přidat výjimky pro servery v této doméně|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpečení sítě: Omezit ntlm: Auditovat ověřování NTLM v této doméně|STROJ\Systém\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Další informace o nastavení registru podporovaném různými verzemi operačního systému naleznete v [referenční tabulce Nastavení zásad skupiny](https://www.microsoft.com/download/confirmation.aspx?id=25250).

*Konfigurace fim pro sledování směrných plánů registru:*

1. V okně **Přidat registr systému Windows pro sledování změn** zadejte do textového pole Klíč registru **systému Windows** klíč registru.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Povolení fim v registru](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Sledování změn souborů systému Windows

1. V okně **Přidat soubor systému Windows pro sledování změn** zadejte do textového pole Enter **cesty** složku obsahující soubory, které chcete sledovat. V příkladu na následujícím obrázku se **contoso Web App** nachází v d:\ jednotky v rámci struktury složek **ContosWebApp.**  
1. Vytvořte vlastní položku souboru systému Windows zadáním názvu třídy nastavení, povolením rekurze a určením horní složky s příponou se zástupnými symboly (*).

    ![Povolení FIM v souboru](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Načítání dat změn

Data monitorování integrity souborů jsou umístěna v sadě tabulek Azure Log Analytics / ConfigurationChange.  

 1. Nastavte časový rozsah pro načtení souhrnu změn podle zdroje.
V následujícím příkladu načítáme všechny změny za posledních čtrnáct dní v kategoriích registru a souborů:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Chcete-li zobrazit podrobnosti o změnách registru:

    1. Odebrat **soubory** z klauzule **kde,** 
    1. Odeberte řádek souhrnu a nahraďte jej klauzulí řazení:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Sestavy lze exportovat do csv pro archivaci a/nebo směrovat do sestavy Power BI.  

![Fim data](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)