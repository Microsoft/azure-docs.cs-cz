---
title: Porovnat standardní hodnoty se monitorování Integrity souborů ve službě Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak porovnat směrné plány se monitorování Integrity souborů ve službě Azure Security Center.
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
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358435"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Porovnat standardní hodnoty pomocí monitorování Integrity souborů (FIM)

Monitorování Integrity souborů (FIM) vás informuje, když dojde ke změnám do citlivých prostor ve vašich prostředcích, abyste mohli prozkoumat a vyřešit neoprávněnou aktivitu. FIM monitoruje soubory Windows, Windows Registry a soubory Linuxu.

Toto téma vysvětluje, jak povolit FIM na soubory a registry. Další informace o produktu FIM, najdete v části [monitorování Integrity souborů ve službě Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Proč používat FIM?

Operační systém, aplikace a přidružené konfigurace řízení chování a stavu zabezpečení vašich prostředků. Proto se útočníci cílí na soubory, které řídí vaše prostředky, aby bylo možné stanovit dřívější operační systém prostředků a/nebo spuštění aktivity bez zjištění.

Ve skutečnosti mnoho standardy dodržování předpisů jako PCI-DSS & ISO 17799 vyžadovat implementaci ovládacích prvků produktu FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Povolit integrované rekurzivní registru kontroly

Výchozí hodnoty registru hive FIM poskytují pohodlný způsob, jak monitorovat rekurzivní změny v rámci běžné oblasti zabezpečení.  Nežádoucí osoba může například nakonfigurovat skript pro spuštění v kontextu LOCAL_SYSTEM podle konfigurace spuštění při spuštění a vypnutí.  Pokud chcete sledovat změny tohoto typu, povolte integrovanou kontrolu.  

![Registru](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekurzivní kontroly platí pouze pro podregistry doporučené zabezpečení a ne na vlastní registru cesty.  

## <a name="adding-a-custom-registry-check"></a>Přidat kontrolu vlastního registru

Směrné plány FIM, začněte tím, že identifikující vlastnosti známému dobrému stavu pro operační systém a aplikace podporuje.  V tomto příkladu se zaměříme na konfigurace zásad hesel systému Windows Server 2008 a vyšší.


|Název zásad                 | Nastavení registru|
|---------------------------------------|-------------|
|Řadič domény: Nepovolit změnu hesla účtu počítače| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Člen domény: Digitálně zašifrovat nebo podepsat data zabezpečeného kanálu (vždy)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|Člen domény: Digitálně zašifrujte data zabezpečeného kanálu (Pokud je to možné)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Člen domény: Digitálně podepsat data zabezpečeného kanálu (Pokud je to možné)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Člen domény: Zakázat změnu hesla účtu počítače|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Člen domény: Doba platnosti hesla účtu počítače maximální|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Člen domény: Vyžadovat silný klíč relace (Windows 2000 nebo novější)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Zabezpečení sítě: Omezte protokol NTLM:  Ověřování protokolem NTLM v této doméně|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|Zabezpečení sítě: Omezte protokol NTLM: Přidat výjimky pro servery v této doméně|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Zabezpečení sítě: Omezte protokol NTLM: Auditovat ověřování protokolem NTLM v této doméně|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Další informace o nastavení registru, které jsou podporovány v různých verzích operačního systému, najdete v tématu [tabulky odkazují na nastavení zásad skupiny](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Ke konfiguraci FIM pro monitorování standardních hodnot registru:*

1. V **přidat registr Windows pro Change Tracking** okno v **klíče registru Windows** textové pole, zadejte klíč registru.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Povolit FIM na registr](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Sledování změn souborů Windows

1. V **přidat soubor Windows pro Change Tracking** okno v **zadejte cestu** text zadejte složky, která obsahuje soubory, které chcete sledovat. V příkladu na následujícím obrázku **webové aplikace Contoso** se nachází v D:\ jednotky v rámci **ContosWebApp** strukturu složek.  
1. Vytvoření vlastní položky souboru Windows poskytnutí názvu třídy nastavení, povolením rekurze a určení složky nejvyšší úrovně s příponou zástupný znak (*).

    ![Povolit FIM na souboru](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Načítání změn dat.

Data se nachází v rámci Azure Log Analytics monitorování Integrity souborů / ConfigurationChange tabulky.  

 1. Nastavte časový rozsah pro načítání souhrn změn prostředků.
V následujícím příkladu načítáme všechny změny za posledních 14 dní v kategoriích registru a souborů:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Chcete-li zobrazit podrobnosti o změnách registru:

    1. Odebrat **soubory** z **kde** klauzule 
    1. Odebrat řádek shrnutí a nahraďte ji metodou klauzuli pořadí:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Sestavy můžete exportovat do souboru CSV pro archivaci nebo channeled do sestavy Power BI.  

![Data produktu FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)