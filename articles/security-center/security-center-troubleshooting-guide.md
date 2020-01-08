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
ms.openlocfilehash: c72357b0e60f36082a468063ecf2bca329cd70be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355308"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Průvodce odstraňováním potíží pro službu Azure Security Center

Tento průvodce je určený odborníkům na informační technologie (IT), analytikům zabezpečení informací a správcům cloudů, jejichž společnosti používají službu Azure Security Center a potřebují odstraňovat potíže týkající se služby Security Center.

Security Center používá Microsoft Monitoring Agent ke shromažďování a ukládání dat. Další informace najdete v článku o [migraci platformy pro Azure Security Center](security-center-platform-migration.md). Informace v tomto článku představují funkce služby Security Center po přechodu na agenta Microsoft Monitoring Agent.

## <a name="troubleshooting-guide"></a>Průvodce řešením potíží

Tento průvodce vysvětluje, jak odstraňovat potíže související se službou Security Center.

Typy výstrah:

* Analýza chování virtuálního počítače (VMBA)
* Analýza sítě
* Analýza SQL Database a SQL Data Warehouse
* Kontextové informace

V závislosti na typech výstrahy mohou uživatelé shromáždit nezbytné informace k prošetření výstrahy pomocí následujících zdrojů:

* Protokoly zabezpečení v prohlížeči událostí virtuálního počítače ve Windows
* AuditD v Linuxu
* Protokoly aktivit Azure a povolte diagnostické protokoly na prostředku útoku.

U některých upozornění Máme také hodnocení spolehlivosti. Skóre spolehlivosti v centru **Security Center** může vašemu týmu pomoci posoudit a určit priority výstrah. **Security Center** automaticky používá osvědčené postupy, inteligentní algoritmy a procesy používané analytiky k určení, jestli je hrozba legitimní a poskytuje smysluplné přehledy ve formě skóre spolehlivosti.

Zákazníci můžou sdílet zpětnou vazbu ohledně popisu a relevance výstrahy. Přejděte na výstrahu samotnou, vyberte tlačítko **Bylo vám to užitečné**, vyberte důvod a zadejte komentář vysvětlující zpětnou vazbu. Tento kanál zpětné vazby neustále sledujeme, abychom naše výstrahy vylepšili.

## <a name="audit-log"></a>Protokol auditu

U většiny postupů odstraňování potíží prováděných v rámci služby Security Center je třeba nejprve si prohlédnout záznamy [protokolu auditu](../azure-monitor/platform/activity-logs-overview.md) pro komponentu, u které se potíže vyskytly. Na základě protokolů auditu můžete zjistit:

* Které operace proběhly
* Kdo operaci zahájil
* Kdy k operaci došlo
* Stav operace
* Hodnoty dalších vlastností, které vám mohou pomoci při zkoumání operace

Protokol auditu obsahuje všechny operace zápisu (PUT, POST, DELETE) prováděné s vašimi prostředky, ale neobsahuje operace čtení (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent

Security Center používá Microsoft Monitoring Agent – jedná se o stejného agenta, kterého používá služba Azure Monitor – ke shromažďování dat zabezpečení z virtuálních počítačů Azure. Po povolení shromažďování dat a řádné instalaci agenta v cílovém počítači by se měl provádět tento proces:

* HealthService.exe

Pokud otevřete konzolu pro správu služeb (services.msc), uvidíte také spuštěnou službu Microsoft Monitoring Agent, jak je znázorněno na následujícím obrázku:

![Služby](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Pokud chcete zjistit, kterou verzi agenta máte, otevřete **Správce úloh**, na kartě **Procesy** vyhledejte **Služba Microsoft Monitoring Agent**, klikněte na ni pravým tlačítkem myši a klikněte na **Vlastnosti**. Na kartě **Podrobnosti** vyhledejte verzi souboru, jak je znázorněno níže:

![Soubor](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scénáře instalace služby Microsoft Monitoring Agent

Existují dva scénáře instalace služby Microsoft Monitoring Agent na počítač, které mohou přinést různé výsledky. Podporované scénáře:

* **Agent nainstalovaný automaticky službou Security Center**: V tomto scénáři se budete moci podívat na výstrahy v obou umístěních – ve službě Security Center i v Hledání v protokolu. Dostanete e-mailová oznámení na e-mailovou adresu, která byla nakonfigurovaná v zásadách zabezpečení pro předplatné, ke kterému prostředek patří.

* **Agent ručně nainstalovaný na virtuálním počítači, který se nachází v Azure**: v tomto scénáři platí, že pokud používáte agenty, kteří se stáhli a nainstalovali ručně před únorem 2017, můžete výstrahy zobrazit na portálu Security Center jenom v případě, že vyfiltrujete předplatné, ke kterému pracovní prostor patří. Pokud filtrujete předplatné, ke kterému prostředek patří, neuvidíte žádné výstrahy. Dostanete e-mailová oznámení na e-mailovou adresu, která byla nakonfigurovaná v zásadách zabezpečení předplatného, ke kterému pracovní prostor patří.

> [!NOTE]
> Pokud se chcete vyhnout chování uvedenému v druhém scénáři, stáhněte si nejnovější verzi agenta.

## Problémy se stavem agenta monitorování <a name="mon-agent"></a>

**Stav monitorování** indikuje důvod, proč se službě Security Center nepodařilo úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování. Seznam hodnot, popisů a postupů řešení pro hodnoty **stavu monitorování** najdete v následující tabulce.

| Stav monitorování | Popis | Postup řešení |
|---|---|---|
| Čeká se na instalaci agenta | Stále probíhá instalace agenta Microsoft Monitoring Agent.  Instalace může trvat i několik hodin. | Počkejte na dokončení automatické instalace. |
| Stav napájení je vypnuto | Virtuální počítač je zastavený.  Agenta Microsoft Monitoring Agent je možné nainstalovat jen na spuštěný virtuální počítač. | Restartujte virtuální počítač. |
| Agent virtuálního počítače Azure chybí nebo není platný | Microsoft Monitoring Agent ještě není nainstalovaný.  Aby služba Security Center mohla nainstalovat rozšíření, potřebuje platného agenta virtuálního počítače Azure. | Nainstalujte, přeinstalujte nebo upgradujte na virtuálním počítači agenta virtuálního počítače Azure. |
| Stav virtuálního počítače není připravený k instalaci  | Microsoft Monitoring Agent ještě není nainstalovaný, protože stav virtuálního počítače brání v jeho instalaci. Virtuální počítač není připraven pro instalaci z důvodu problému s agentem nebo zřizováním virtuálního počítače. | Zkontrolujte stav virtuálního počítače. Vraťte se na portálu na obrazovku **Virtual Machines** a výběrem virtuálního počítače zobrazte jeho stav. |
|Instalace selhala – obecná chyba | Microsoft Monitoring Agent je nainstalovaný, ale selhal kvůli chybě. | [Ručně rozšíření nainstalujte](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) nebo odinstalujte, aby se ho služba Security Center mohla pokusit znovu nainstalovat. |
| Instalace selhala – místní agent je už nainstalovaný | Instalace nástroje Microsoft Monitoring Agent selhala. Security Center identifikovali místního agenta (Log Analytics nebo System Center Operations Manager), který je už na virtuálním počítači nainstalovaný. Aby se zabránilo vícenásobné konfiguraci, kdy se virtuální počítač hlásí dvěma samostatným pracovním prostorům, instalace agenta Microsoft Monitoring Agent byla zastavena. | Je možné to vyřešit dvěma způsoby: [nainstalovat rozšíření ručně](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) a připojit ho do požadovaného pracovního prostoru. Nebo nastavit požadovaný pracovní prostor jako výchozí a povolit automatické zřizování agenta.  Viz [zapnutí automatického zřizování](security-center-enable-data-collection.md). |
| Agent se nemůže připojit k pracovnímu prostoru | Microsoft Monitoring Agent je nainstalovaný, ale selhal kvůli chybě připojení k síti.  Zkontrolujte připojení k internetu a jestli je pro agenta nakonfigurovaný správný proxy server HTTP. | Viz požadavky na síť agenta monitorování. |
| Agent je připojený do chybějícího nebo neznámého pracovního prostoru | Služba Security Center zjistila, že Microsoft Monitoring Agent instalovaný na virtuálním počítači je připojený k pracovnímu prostoru, do kterého nemá přístup. | K tomu může dojít ve dvou případech. Pracovní prostor byl odstraněn a už neexistuje. Znovu nainstalujte agenta s použitím správného pracovního prostoru nebo agenta odinstalujte a povolte službě Security Center provést jeho instalaci pomocí automatického zřizování. Druhou možností je, že pracovní prostor patří do předplatného, pro které nemá Security Center oprávnění. Security Center vyžaduje, aby předplatné povolovalo přístup zprostředkovateli služby Microsoft Security Resource Provider. Napravíte to tak, že dané předplatné zaregistrujete do služby Microsoft Security Resource Provider. Můžete to udělat přes rozhraní API, v prostředí PowerShell, na portálu nebo jednoduše pomocí filtrování podle předplatného na řídicím panelu **Přehled** služby Security Center. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Agent neodpovídá nebo chybí ID | Security Center nemůže z virtuálního počítače načíst shromážděná data zabezpečení, přestože je agent nainstalovaný. | Agent nevrací žádná data, a to ani prezenční signál. Agent může být poškozený nebo něco blokuje provoz. Agent možná odesílá data, ale chybí mu ID prostředku Azure, takže není možné data přiřadit správnému virtuálnímu počítači. Řešení problémů v Linuxu, naleznete v tématu [Průvodce odstraňováním potíží pro agenta Log Analytics pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Informace o řešení problémů ve Windows najdete v tématu [Odstraňování problémů virtuálních počítačů Windows](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agent nenainstalován | Shromažďování dat je vypnuté. | Zapněte shromažďování dat v zásadách zabezpečení nebo nainstalujte agenta Microsoft Monitoring Agent ručně. |

## Řešení potíží se síťovými požadavky na agenta monitorování <a name="mon-network-req"></a>

Agenti, kteří se připojují ke službě Security Center a registrují se v ní, musí mít přístup k síťovým prostředkům, včetně čísel portů a doménových adres URL.

* U proxy serverů musíte zajistit konfiguraci příslušných prostředků proxy serveru v nastavení agenta. Přečtěte si tento článek, ve kterém se dozvíte další informace o [tom, jak změnit nastavení proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents).
* Pokud používáte k omezení přístupu k internetu bránu firewall, je nutné ji nakonfigurovat tak, aby povolovala přístup k Log Analytics. V nastavení agenta nemusíte nic konfigurovat.

V následující tabulce najdete přehled prostředků potřebných pro komunikaci.

| Prostředek agenta | Porty | Obejít kontrolu protokolu HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ano |
| *.oms.opinsights.azure.com | 443 | Ano |
| *.blob.core.windows.net | 443 | Ano |
| *.azure-automation.net | 443 | Ano |

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

Některé potíže lze identifikovat podle pokynů v tomto článku, některé další jsou také dokumentovány ve veřejném [fóru](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) služby Security Center. Pokud však potřebujete odstraňovat potíže mimo tento rámec, můžete vytvořit novou žádost o podporu prostřednictvím webu **Azure Portal**, jak je znázorněno níže:

![Podpora Microsoftu](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Další informace najdete v tématech

V tomto dokumentu jste zjistili, jak ve službě Azure Security Center konfigurovat zásady zabezpečení. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně
* [Principy výstrah zabezpečení ve službě Azure Security Center](security-center-alerts-type.md).
* [Kurz: Reakce na incidenty zabezpečení](tutorial-security-incident.md)
* [Ověřování výstrah ve službě Azure Security Center](security-center-alert-validation.md)
* [E-mailová oznámení ve službě Azure Security Center](security-center-provide-security-contact-details.md)
* [Řešení bezpečnostních incidentů ve službě Azure Security Center](security-center-incident.md)
* [Skóre spolehlivosti výstrah](security-center-secure-score.md)
* [Vyšetřování incidentů a výstrah ve službě Azure Security Center](security-center-investigation.md)
* [Možnosti detekce v Azure Security Center](security-center-detection-capabilities.md)
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů
