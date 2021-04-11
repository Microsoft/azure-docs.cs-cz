---
title: Zabezpečení dat Azure Automation
description: Tento článek vám pomůže zjistit, jak Azure Automation chrání vaše osobní údaje a zabezpečuje vaše data.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: c3d1dfc5d6ea16a128f5f3bc1129f5f50bc9cb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954669"
---
# <a name="management-of-azure-automation-data"></a>Správa dat služby Azure Automation

Tento článek obsahuje několik témat vysvětlujících, jak jsou data chráněná a zabezpečená v Azure Automationm prostředí.

## <a name="tls-12-enforcement-for-azure-automation"></a>Vynucení TLS 1,2 pro Azure Automation

Aby se zajistilo zabezpečení dat při přenosu do Azure Automation, důrazně doporučujeme, abyste nakonfigurovali použití TLS (Transport Layer Security) 1,2. Níže najdete seznam metod nebo klientů, které komunikují přes HTTPS do služby Automation:

* Volání Webhooku

* Hybridní pracovní procesy Runbooku, mezi které patří počítače spravované pomocí Update Management a Change Tracking a inventáře.

* Uzly DSC

Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje** se. Nedoporučujeme explicitně nastavit agenta tak, aby používal pouze TLS 1,2, pokud to není nutné, protože může přerušit funkce zabezpečení na úrovni platformy, které vám umožní automaticky zjišťovat a využívat novější bezpečnější protokoly, když jsou k dispozici, jako je třeba TLS 1,3.

Informace o podpoře TLS 1,2 s agentem Log Analytics pro systémy Windows a Linux, což je závislost pro roli Hybrid Runbook Worker, najdete v tématu [Přehled agenta Log Analytics-TLS 1,2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Doprovodné materiály pro konkrétní platformu

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
|Linux | Distribuce systému Linux se obvykle spoléhají na [OpenSSL](https://www.openssl.org) pro podporu TLS 1,2.  | Zkontrolujte [OpenSSL protokolu změn](https://www.openssl.org/news/changelog.html) a potvrďte, že je podporovaná vaše verze OpenSSL.|
| Windows 8,0 – 10 | Podporované a povolené ve výchozím nastavení. | Potvrďte, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporované a povolené ve výchozím nastavení. | Potvrzení, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporované, ale nejsou ve výchozím nastavení povolené. | Podrobnosti o tom, jak povolit, najdete na stránce [nastavení registru TLS (Transport Layer Security)](/windows-server/security/tls/tls-registry-settings) .  |

## <a name="data-retention"></a>Uchovávání dat

Když prostředek odstraníte z Azure Automation, bude se po celou dobu od trvalého odebrání uchovávat spousta dní. Během této doby nemůžete tento prostředek zobrazit ani použít. Tyto zásady platí taky pro prostředky, které patří k odstraněnému účtu Automation. Zásady uchovávání informací platí pro všechny uživatele a aktuálně se nedají přizpůsobit. Pokud ale potřebujete zachovat data po delší dobu, můžete [data Azure Automation úlohy přeposílat do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).

Následující tabulka shrnuje zásady uchovávání informací pro různé prostředky.

| Data | Zásady |
|:--- |:--- |
| Účty |Účet se trvale odebere 30 dní poté, co ho uživatel odstraní. |
| Prostředky |Asset se trvale odebere 30 dní poté, co ho uživatel odstraní nebo 30 dní poté, co uživatel odstraní účet, který má Asset. Prostředky zahrnují proměnné, plány, přihlašovací údaje, certifikáty, balíčky Python 2 a připojení. |
| Uzly DSC |Uzel DSC se trvale odebere 30 dnů po zrušení registrace účtu Automation pomocí Azure Portal nebo rutiny [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) ve Windows PowerShellu. Uzel je také trvale odebrán 30 dní poté, co uživatel odstraní účet, který obsahuje uzel. |
| Úlohy |Úloha je odstraněna a trvale odebrána 30 dní po provedení změny, například po dokončení úlohy, zastavení nebo pozastavení. |
| Moduly |Modul se trvale odebere 30 dní poté, co ho uživatel odstraní nebo 30 dní poté, co uživatel odstraní účet, který obsahuje daný modul. |
| Konfigurace uzlů/soubory MOF |Stará konfigurace uzlu je trvale odebrána 30 dní po vygenerování nové konfigurace uzlu. |
| Sestavy uzlů |Po vygenerování nové sestavy pro tento uzel je sestava uzlu trvale odebrána 90 dní. |
| Runbooky |Sada Runbook je trvale odebrána 30 dní poté, co uživatel odstraní prostředek, nebo 30 dní poté, co uživatel odstraní účet, který obsahuje prostředek<sup>1</sup>. |

<sup>1</sup> Sada Runbook se dá obnovit v rámci 30denní okna tím, že pomůžete incident podpory Azure s podporou Microsoft Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **Odeslat žádost o podporu**.

## <a name="data-backup"></a>Zálohování dat

Když odstraníte účet služby Automation v Azure, odstraní se všechny objekty v účtu. Mezi tyto objekty patří Runbooky, moduly, konfigurace, nastavení, úlohy a prostředky. Po odstranění účtu už je nepůjde obnovit. Následující informace můžete použít k zálohování obsahu svého účtu Automation před jeho odstraněním.

### <a name="runbooks"></a>Runbooky

Runbooky můžete exportovat do souborů skriptu pomocí rutiny Azure Portal nebo rutiny [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) ve Windows PowerShellu. Tyto soubory skriptu můžete importovat do jiného účtu Automation, jak je popsáno v tématu [Správa runbooků v Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integrační moduly

Nemůžete exportovat moduly integrace z Azure Automation, musí být zpřístupněny mimo účet Automation.

### <a name="assets"></a>Prostředky

Nemůžete exportovat Azure Automation assety: certifikáty, připojení, přihlašovací údaje, plány a proměnné. Místo toho můžete použít rutiny Azure Portal a Azure a poznamenat si podrobnosti o těchto prostředcích. Pak pomocí těchto podrobností vytvořte všechny prostředky, které používají Runbooky, které importujete do jiného účtu Automation.

Pomocí rutin se nedají načíst hodnoty šifrovaných proměnných nebo polí s hesly přihlašovacích údajů. Pokud tyto hodnoty neznáte, můžete je načíst v Runbooku. Informace o načítání hodnot proměnných naleznete [v tématu Variable assets in Azure Automation](shared-resources/variables.md). Další informace o načítání hodnot přihlašovacích údajů najdete v tématu [assety přihlašovacích údajů v Azure Automation](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>Konfigurace DSC

Konfigurace DSC můžete exportovat do souborů skriptu pomocí Azure Portal nebo rutiny [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) ve Windows PowerShellu. Tyto konfigurace můžete importovat a používat v jiném účtu Automation.

## <a name="geo-replication-in-azure-automation"></a>Geografická replikace v Azure Automation

Geografická replikace je v účtech Azure Automation Standard. Při nastavování účtu se volí primární oblast. Interní služba pro geografickou replikaci služby Automation přiřadí k účtu sekundární oblast automaticky. Služba pak průběžně zálohuje data účtu z primární oblasti do sekundární oblasti. Úplný seznam primárních a sekundárních oblastí najdete v části [provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure](../best-practices-availability-paired-regions.md).

Záloha vytvořená geografickou replikací služby Automation je kompletní kopie automatizačních prostředků, konfigurací a podobně. Tato záloha se dá použít, pokud primární oblast rozroste a ztratí data. V nepravděpodobném případě, že dojde ke ztrátě dat pro primární oblast, se Microsoft pokusí ji obnovit.

> [!NOTE]
> Azure Automation ukládá zákaznická data v oblasti vybrané zákazníkem. Pro účely BCDR se pro všechny oblasti kromě Brazílie – jih a jihovýchodní Asie budou data Azure Automation ukládat v jiné oblasti (v spárované oblasti Azure). Jenom pro oblast Brazílie – jih (Svatý Paulo State) v oblasti Brazílie – jih a jihovýchodní Asie (Singapur) Asie a Tichomoří geografie ukládáme Azure Automation data ve stejné oblasti, aby se pro tyto oblasti vešly požadavky na umístění dat.

Služba geografického replikace pro automatizaci není dostupná přímo pro externí zákazníky, pokud dojde k místnímu selhání. Pokud chcete zachovat konfiguraci a runbooky automatizace během regionálních selhání:

1. Vyberte sekundární oblast, která se má spárovat s geografickou oblastí vašeho primárního účtu Automation.

2. Vytvořte účet Automation v sekundární oblasti.

3. V primárním účtu exportujte Runbooky jako soubory skriptu.

4. Importujte Runbooky do svého účtu Automation v sekundární oblasti.

## <a name="next-steps"></a>Další kroky

* Další informace o zabezpečených assetech v Azure Automation najdete v tématu [šifrování zabezpečených prostředků v Azure Automation](automation-secure-asset-encryption.md).

* Další informace o geografické replikaci najdete v tématu [vytváření a používání aktivní geografické replikace](../azure-sql/database/active-geo-replication-overview.md).
