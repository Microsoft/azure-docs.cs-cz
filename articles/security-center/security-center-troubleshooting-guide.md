---
title: Průvodce odstraňováním potíží se službou Azure Security Center | Dokumentace Microsoftu
description: Tato příručka je určena pro profesionály v oblasti IT, analytikům zabezpečení a cloudovým správcům, kteří potřebují řešit problémy související s Azure Security Center.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: f5e71697ca6ce9e2585bbb903ad8c46744e05e13
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462390"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Průvodce odstraňováním potíží pro službu Azure Security Center

Tento průvodce je určený odborníkům na informační technologie (IT), analytikům zabezpečení informací a správcům cloudů, jejichž společnosti používají službu Azure Security Center a potřebují odstraňovat potíže týkající se služby Security Center.

Security Center používá agenta Log Analytics ke shromažďování a ukládání dat. Další informace najdete v článku o [migraci platformy pro Azure Security Center](security-center-platform-migration.md). Informace v tomto článku představují Security Center funkce po přechodu na agenta Log Analytics.

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tento průvodce vysvětluje, jak odstraňovat potíže související se službou Security Center.

Typy výstrah:

* Analýza chování virtuálního počítače (VMBA)
* Analýza sítě
* Analýza SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse)
* Kontextové informace

V závislosti na typech výstrahy mohou uživatelé shromáždit nezbytné informace k prošetření výstrahy pomocí následujících zdrojů:

* Protokoly zabezpečení v prohlížeči událostí virtuálního počítače ve Windows
* AuditD v Linuxu
* Protokoly aktivit Azure a povolení diagnostických protokolů u zdroje útoku.

Zákazníci můžou sdílet zpětnou vazbu ohledně popisu a relevance výstrahy. Přejděte na výstrahu samotnou, vyberte tlačítko **Bylo vám to užitečné**, vyberte důvod a zadejte komentář vysvětlující zpětnou vazbu. Tento kanál zpětné vazby neustále sledujeme, abychom naše výstrahy vylepšili.

## <a name="audit-log"></a>Protokol auditu

U většiny postupů odstraňování potíží prováděných v rámci služby Security Center je třeba nejprve si prohlédnout záznamy [protokolu auditu](../azure-monitor/platform/platform-logs-overview.md) pro komponentu, u které se potíže vyskytly. Na základě protokolů auditu můžete zjistit:

* Které operace proběhly
* Kdo operaci zahájil
* Kdy k operaci došlo
* Stav operace
* Hodnoty dalších vlastností, které vám mohou pomoci při zkoumání operace

Protokol auditu obsahuje všechny operace zápisu (PUT, POST, DELETE) prováděné s vašimi prostředky, ale neobsahuje operace čtení (GET).

## <a name="log-analytics-agent"></a>Agent Log Analytics

Security Center používá agenta Log Analytics – to je stejný agent, kterého používá služba Azure Monitor – ke shromažďování dat zabezpečení z virtuálních počítačů Azure. Po povolení shromažďování dat a řádné instalaci agenta v cílovém počítači by se měl provádět tento proces:

* HealthService.exe

Pokud otevřete konzolu pro správu služeb (Services. msc), zobrazí se také služba agenta Log Analytics spuštěná, jak je znázorněno níže:

![Služby](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Pokud chcete zjistit, kterou verzi agenta máte, otevřete **Správce úloh**, na kartě **procesy** Najděte **službu Log Analytics agent**, klikněte na ni pravým tlačítkem myši a klikněte na **vlastnosti**. Na kartě **Podrobnosti** vyhledejte verzi souboru, jak je znázorněno níže:

![Soubor](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Scénáře instalace agenta Log Analytics

Existují dva scénáře instalace, které mohou při instalaci agenta Log Analytics do počítače způsobit různé výsledky. Podporované scénáře:

* **Agent nainstalovaný automaticky službou Security Center**: V tomto scénáři se budete moci podívat na výstrahy v obou umístěních – ve službě Security Center i v Hledání v protokolu. Dostanete e-mailová oznámení na e-mailovou adresu, která byla nakonfigurovaná v zásadách zabezpečení pro předplatné, ke kterému prostředek patří.

* **Agent ručně nainstalovaný na virtuálním počítači, který se nachází v Azure**: v tomto scénáři platí, že pokud používáte agenty, kteří se stáhli a nainstalovali ručně před únorem 2017, můžete výstrahy zobrazit na portálu Security Center jenom v případě, že vyfiltrujete předplatné, ke kterému pracovní prostor patří. Pokud filtrujete předplatné, ke kterému prostředek patří, neuvidíte žádné výstrahy. Dostanete e-mailová oznámení na e-mailovou adresu, která byla nakonfigurovaná v zásadách zabezpečení předplatného, ke kterému pracovní prostor patří.

> [!NOTE]
> Pokud se chcete vyhnout chování uvedenému v druhém scénáři, stáhněte si nejnovější verzi agenta.

## <a name="monitoring-agent-health-issues"></a>Problémy s monitorováním stavu agentů <a name="mon-agent"></a>

**Stav monitorování** indikuje důvod, proč se službě Security Center nepodařilo úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování. Seznam hodnot, popisů a postupů řešení pro hodnoty **stavu monitorování** najdete v následující tabulce.

| Stav monitorování | Popis | Postup řešení |
|---|---|---|
| Čeká se na instalaci agenta | Instalace agenta Log Analytics stále běží.  Instalace může trvat i několik hodin. | Počkejte na dokončení automatické instalace. |
| Stav napájení je vypnuto | Virtuální počítač je zastavený.  Agent Log Analytics může být nainstalovaný jenom na virtuálním počítači, na kterém běží. | Restartujte virtuální počítač. |
| Agent virtuálního počítače Azure chybí nebo není platný | Agent Log Analytics ještě není nainstalovaný.  Aby služba Security Center mohla nainstalovat rozšíření, potřebuje platného agenta virtuálního počítače Azure. | Nainstalujte, přeinstalujte nebo upgradujte na virtuálním počítači agenta virtuálního počítače Azure. |
| Stav virtuálního počítače není připravený k instalaci  | Agent Log Analytics ještě není nainstalovaný, protože virtuální počítač není připravený k instalaci. Virtuální počítač není připraven pro instalaci z důvodu problému s agentem nebo zřizováním virtuálního počítače. | Zkontrolujte stav virtuálního počítače. Vraťte se na portálu na obrazovku **Virtual Machines** a výběrem virtuálního počítače zobrazte jeho stav. |
|Instalace selhala – obecná chyba | Agent Log Analytics byl nainstalován, ale selhal z důvodu chyby. | [Ručně rozšíření nainstalujte](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) nebo odinstalujte, aby se ho služba Security Center mohla pokusit znovu nainstalovat. |
| Instalace selhala – místní agent je už nainstalovaný | Nepovedlo se nainstalovat agenta Log Analytics. Security Center identifikovali místního agenta (Log Analytics nebo System Center Operations Manager), který je už na virtuálním počítači nainstalovaný. Aby se zabránilo konfiguraci více domovských stránek, kde se virtuální počítač hlásí do dvou samostatných pracovních prostorů, instalace agenta Log Analytics se zastavila. | Je možné to vyřešit dvěma způsoby: [nainstalovat rozšíření ručně](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) a připojit ho do požadovaného pracovního prostoru. Nebo nastavit požadovaný pracovní prostor jako výchozí a povolit automatické zřizování agenta.  Viz [zapnutí automatického zřizování](security-center-enable-data-collection.md). |
| Agent se nemůže připojit k pracovnímu prostoru | Agent Log Analytics se nainstaloval, ale selhal kvůli připojení k síti.  Zkontrolujte připojení k internetu a jestli je pro agenta nakonfigurovaný správný proxy server HTTP. | Viz požadavky agenta monitorování na síť. |
| Agent je připojený do chybějícího nebo neznámého pracovního prostoru | Security Center zjistila, že agent Log Analytics nainstalovaný na virtuálním počítači je připojený k pracovnímu prostoru, ke kterému nemá přístup. | K tomu může dojít ve dvou případech. Pracovní prostor byl odstraněn a už neexistuje. Znovu nainstalujte agenta s použitím správného pracovního prostoru nebo agenta odinstalujte a povolte službě Security Center provést jeho instalaci pomocí automatického zřizování. Druhou možností je, že pracovní prostor patří do předplatného, pro které nemá Security Center oprávnění. Security Center vyžaduje, aby předplatné povolovalo přístup zprostředkovateli služby Microsoft Security Resource Provider. Napravíte to tak, že dané předplatné zaregistrujete do služby Microsoft Security Resource Provider. Můžete to udělat přes rozhraní API, v prostředí PowerShell, na portálu nebo jednoduše pomocí filtrování podle předplatného na řídicím panelu **Přehled** služby Security Center. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Agent neodpovídá nebo chybí ID | Security Center nemůže z virtuálního počítače načíst shromážděná data zabezpečení, přestože je agent nainstalovaný. | Agent nevrací žádná data, a to ani prezenční signál. Agent může být poškozený nebo něco blokuje provoz. Nebo agent hlásí data, ale chybí ID prostředku Azure, takže není možné, aby se data shodovala s virtuálním počítačem Azure. Řešení potíží s Linuxem najdete v tématu [Průvodce odstraňováním potíží pro Log Analytics agenta pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Informace o řešení problémů ve Windows najdete v tématu [Odstraňování problémů virtuálních počítačů Windows](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agent nenainstalován | Shromažďování dat je vypnuté. | Zapněte shromažďování dat v zásadách zabezpečení nebo ručně nainstalujte agenta Log Analytics. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Řešení potíží se síťovými požadavky na agenta monitorování <a name="mon-network-req"></a>

Agenti, kteří se připojují ke službě Security Center a registrují se v ní, musí mít přístup k síťovým prostředkům, včetně čísel portů a doménových adres URL.

* U proxy serverů musíte zajistit konfiguraci příslušných prostředků proxy serveru v nastavení agenta. Přečtěte si tento článek, ve kterém se dozvíte další informace o [tom, jak změnit nastavení proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
* Pokud používáte k omezení přístupu k internetu bránu firewall, je nutné ji nakonfigurovat tak, aby povolovala přístup k Log Analytics. V nastavení agenta nemusíte nic konfigurovat.

V následující tabulce najdete přehled prostředků potřebných pro komunikaci.

| Prostředek agenta | Porty | Obejít kontrolu protokolu HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Yes |
| *.oms.opinsights.azure.com | 443 | Yes |
| *.blob.core.windows.net | 443 | Yes |
| *.azure-automation.net | 443 | Yes |

Pokud narazíte na problémy s registrací agenta, přečtěte si článek [Řešení potíží s registrací v Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Řešení potíží s tím, že ochrana koncových bodů nefunguje správně

Agent hosta je nadřazený proces pro všechno, co dělá rozšíření [Microsoft Antimalware](../security/fundamentals/antimalware.md). Pokud proces agenta hosta selže, může se stát, že selže i Microsoft Antimalware, který je spuštěný jako jeho podřízený proces.  V podobných situacích se doporučuje ověřit následující možnosti:

* Pokud je cílový virtuální počítač založený na vlastní imagi a tvůrce virtuálního počítače nikdy nenainstaloval agent hosta.
* Pokud cílový virtuální počítač místo Windows používá Linux, instalace verze antimalwarového rozšíření pro Windows na linuxovém virtuálním počítači selže. Linuxový agent hosta má specifické požadavky na verzi operačního systému a požadované balíčky. Pokud tyto požadavky nejsou splněny, agent virtuálního počítače také nebude fungovat.
* Pokud se při vytvoření virtuálního počítače použila stará verze agenta hosta. Pokud ano, měli byste vědět, že u některých starých agentů nefunguje automatická aktualizace na novější verzi a to by mohlo vést k tomuto problému. Pokud vytváříte vlastní image, vždy používejte nejnovější verzi agenta hosta.
* Některé softwary pro správu třetích stran mohou zakázat agenta hosta nebo blokovat přístup k určitým umístěním souborů. Pokud máte ve virtuálním počítači nainstalovaný software třetích stran, ujistěte se, že agent je uvedený v seznamu vyloučení.
* Určitá nastavení brány firewall nebo skupiny zabezpečení sítě (NSG) mohou zablokovat síťový provoz do a z agenta hosta.
* Některé seznamy řízení přístupu (ACL) mohou bránit v přístupu k disku.
* Nedostatek místa na disku může blokovat agent hosta a ten nebude fungovat správně.

Ve výchozím nastavení je uživatelské rozhraní rozšíření Microsoft Antimalware zablokované. Další informace o tom, jak ho v případě potřeby povolit, najdete v tématu věnovaném [povolení uživatelského rozhraní rozšíření Microsoft Antimalware ve virtuálních počítačích s Azure Resource Managerem po nasazení](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/).

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Odstraňování potíží s načtením řídicího panelu

Pokud dochází k problémům s načtením řídicího panelu služby Security Center, ujistěte se, že uživatel, který předplatné ke službě Security Center registruje (tj. uživatel, který s tímto předplatným otevřel službu Security Center) a uživatel, který chce zapnout shromažďování dat, mají u daného předplatného roli *Vlastník* nebo *Přispěvatel*. Od této chvíle budou moci i uživatelé, kteří u předplatného mají roli *Čtenář*, zobrazovat řídicí panel, upozornění, doporučení a zásady.

## <a name="contacting-microsoft-support"></a>Kontaktování oddělení podpory společnosti Microsoft

Některé problémy je možné identifikovat pomocí pokynů uvedených v tomto článku, další informace najdete také na stránce s otázkou Security Center Public [&Microsoft Q](https://docs.microsoft.com/answers/topics/azure-security-center.html). Pokud však potřebujete další řešení potíží, můžete otevřít novou žádost o podporu pomocí **Azure Portal** , jak je znázorněno níže:

![Podpora společnosti Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Viz také

V tomto dokumentu jste zjistili, jak ve službě Azure Security Center konfigurovat zásady zabezpečení. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Principy výstrah zabezpečení ve službě Azure Security Center](security-center-alerts-type.md)
* [Kurz: Reakce na incidenty zabezpečení](tutorial-security-incident.md)
* [Ověřování výstrah ve službě Azure Security Center](security-center-alert-validation.md)
* [E-mailová oznámení ve službě Azure Security Center](security-center-provide-security-contact-details.md)
* [Řešení bezpečnostních incidentů v Azure Security Center](security-center-incident.md)
* [Možnosti detekce Azure Security Center](security-center-detection-capabilities.md)
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](faq-general.md) – Přečtěte si nejčastější dotazy o použití této služby
* [Blog o zabezpečení Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů
