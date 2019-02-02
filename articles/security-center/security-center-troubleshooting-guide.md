---
title: Průvodce odstraňováním potíží se službou Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže při odstraňování potíží se službou Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2018
ms.author: rkarlin
ms.openlocfilehash: 0c2bef66e0570db37a2264e72fe46e619ef7db75
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565441"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Průvodce odstraňováním potíží pro službu Azure Security Center
Tento průvodce je určený odborníkům na informační technologie (IT), analytikům zabezpečení informací a správcům cloudů, jejichž společnosti používají službu Azure Security Center a potřebují odstraňovat potíže týkající se služby Security Center.

>[!NOTE]
>Od začátku června 2017 používá Security Center ke shromažďování a ukládání dat agenta Microsoft Monitoring Agent. Další informace najdete v článku o [migraci platformy pro Azure Security Center](security-center-platform-migration.md). Informace v tomto článku představují funkce služby Security Center po přechodu na agenta Microsoft Monitoring Agent.
>

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Tento průvodce vysvětluje, jak odstraňovat potíže související se službou Security Center. U většiny postupů odstraňování potíží prováděných v rámci služby Security Center je třeba nejprve si prohlédnout záznamy [protokolu auditu](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) pro komponentu, u které se potíže vyskytly. Na základě protokolů auditu můžete zjistit:

* Které operace proběhly
* Kdo operaci zahájil
* Kdy k operaci došlo
* Stav operace
* Hodnoty dalších vlastností, které vám mohou pomoci při zkoumání operace

Protokol auditu obsahuje všechny operace zápisu (PUT, POST, DELETE) prováděné s vašimi prostředky, ale neobsahuje operace čtení (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Security Center používá agenta Microsoft Monitoring Agent – to je stejný agent, kterého používá služba Log Analytics – ke shromažďování dat zabezpečení z virtuálních počítačů Azure. Po povolení shromažďování dat a řádné instalaci agenta v cílovém počítači by se měl provádět tento proces:

* HealthService.exe

Pokud otevřete konzolu pro správu služeb (services.msc), uvidíte také spuštěnou službu Microsoft Monitoring Agent, jak je znázorněno na následujícím obrázku:

![Služby](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Pokud chcete zjistit, kterou verzi agenta máte, otevřete **Správce úloh**, na kartě **Procesy** vyhledejte **Služba Microsoft Monitoring Agent**, klikněte na ni pravým tlačítkem myši a klikněte na **Vlastnosti**. Na kartě **Podrobnosti** vyhledejte verzi souboru, jak je znázorněno níže:

![File](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)


## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scénáře instalace služby Microsoft Monitoring Agent
Existují dva scénáře instalace služby Microsoft Monitoring Agent na počítač, které mohou přinést různé výsledky. Podporované scénáře:

* **Agent nainstalovaný automaticky službou Security Center**: V tomto scénáři se budete moci podívat na výstrahy v obou umístěních – ve službě Security Center i v Hledání v protokolu. E-mailová oznámení budete dostávat na e-mailovou adresu, kterou jste si nakonfigurovali v zásadách zabezpečení pro předplatné, do něhož prostředek patří.
.
* **Agent ručně nainstalovaný na virtuálním počítači umístěném v Azure**: V tomto scénáři – pokud používáte agenty, které jste stáhli a nainstalovali ručně před únorem 2017 – se budete moci podívat na výstrahy na portálu Security Center jenom v případě, že si vyfiltrujete předplatné, do kterého pracovní prostor patří. Pokud si vyfiltrujete předplatné, do kterého prostředek patří, neuvidíte žádné jiné výstrahy. E-mailová oznámení budete dostávat na e-mailovou adresu, kterou jste si nakonfigurovali v zásadách zabezpečení pro předplatné, do něhož pracovní prostor patří.

>[!NOTE]
> Pokud se chcete vyhnout chování uvedenému v druhém scénáři, stáhněte si nejnovější verzi agenta.
>

## Problémy se stavem agenta monitorování <a name="mon-agent"></a>
**Stav monitorování** indikuje důvod, proč se službě Security Center nepodařilo úspěšně monitorovat virtuální počítače a počítače inicializované pro automatické zřizování. Seznam hodnot, popisů a postupů řešení pro hodnoty **stavu monitorování** najdete v následující tabulce.

| Stav monitorování | Popis | Postup řešení |
|---|---|---|
| Čeká se na instalaci agenta | Stále probíhá instalace agenta Microsoft Monitoring Agent.  Instalace může trvat i několik hodin. | Počkejte na dokončení automatické instalace. |
| Stav napájení je vypnuto | Virtuální počítač je zastavený.  Agenta Microsoft Monitoring Agent je možné nainstalovat jen na spuštěný virtuální počítač. | Restartujte virtuální počítač. |
| Agent virtuálního počítače Azure chybí nebo není platný | Microsoft Monitoring Agent ještě není nainstalovaný.  Aby služba Security Center mohla nainstalovat rozšíření, potřebuje platného agenta virtuálního počítače Azure. | Nainstalujte, přeinstalujte nebo upgradujte na virtuálním počítači agenta virtuálního počítače Azure. |
| Stav virtuálního počítače není připravený k instalaci  | Microsoft Monitoring Agent ještě není nainstalovaný, protože stav virtuálního počítače brání v jeho instalaci. Virtuální počítač není připraven pro instalaci z důvodu problému s agentem nebo zřizováním virtuálního počítače. | Zkontrolujte stav virtuálního počítače. Vraťte se na portálu na obrazovku **Virtual Machines** a výběrem virtuálního počítače zobrazte jeho stav. |
|Instalace selhala – obecná chyba | Microsoft Monitoring Agent je nainstalovaný, ale selhal kvůli chybě. | [Ručně rozšíření nainstalujte](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) nebo odinstalujte, aby se ho služba Security Center mohla pokusit znovu nainstalovat. |
| Instalace selhala – místní agent je už nainstalovaný | Instalace nástroje Microsoft Monitoring Agent selhala. Služba Security Center zjistila místní agent (Log Analytics nebo SCOM) už nainstalovaná na virtuálním počítači. Aby se zabránilo vícenásobné konfiguraci, kdy se virtuální počítač hlásí dvěma samostatným pracovním prostorům, instalace agenta Microsoft Monitoring Agent byla zastavena. | Je možné to vyřešit dvěma způsoby: [nainstalovat rozšíření ručně](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) a připojit ho do požadovaného pracovního prostoru. Nebo nastavit požadovaný pracovní prostor jako výchozí a povolit automatické zřizování agenta.  Viz [zapnutí automatického zřizování](security-center-enable-data-collection.md). |
| Agent se nemůže připojit k pracovnímu prostoru | Microsoft Monitoring Agent je nainstalovaný, ale selhal kvůli chybě připojení k síti.  Zkontrolujte připojení k internetu a jestli je pro agenta nakonfigurovaný správný proxy server HTTP. | Viz [požadavky agenta monitorování na síť](#troubleshooting-monitoring-agent-network-requirements). |
| Agent je připojený do chybějícího nebo neznámého pracovního prostoru | Služba Security Center zjistila, že Microsoft Monitoring Agent instalovaný na virtuálním počítači je připojený k pracovnímu prostoru, do kterého nemá přístup. | K tomu může dojít ve dvou případech. Pracovní prostor byl odstraněn a už neexistuje. Znovu nainstalujte agenta s použitím správného pracovního prostoru nebo agenta odinstalujte a povolte službě Security Center provést jeho instalaci pomocí automatického zřizování. Druhou možností je, že pracovní prostor patří do předplatného, pro které nemá Security Center oprávnění. Security Center vyžaduje, aby předplatné povolovalo přístup zprostředkovateli služby Microsoft Security Resource Provider. Napravíte to tak, že dané předplatné zaregistrujete do služby Microsoft Security Resource Provider. Můžete to udělat přes rozhraní API, v prostředí PowerShell, na portálu nebo jednoduše pomocí filtrování podle předplatného na řídicím panelu **Přehled** služby Security Center. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/resource-manager-supported-services.md#azure-portal). |
| Agent neodpovídá nebo chybí ID | Security Center nemůže z virtuálního počítače načíst shromážděná data zabezpečení, přestože je agent nainstalovaný. | Agent nevrací žádná data, a to ani prezenční signál. Agent může být poškozený nebo něco blokuje provoz. Agent možná odesílá data, ale chybí mu ID prostředku Azure, takže není možné data přiřadit správnému virtuálnímu počítači. Řešení problémů v Linuxu, naleznete v tématu [Průvodce odstraňováním potíží pro agenta Log Analytics pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Informace o řešení problémů ve Windows najdete v tématu [Odstraňování problémů virtuálních počítačů Windows](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agent nenainstalován | Shromažďování dat je vypnuté. | Zapněte shromažďování dat v zásadách zabezpečení nebo nainstalujte agenta Microsoft Monitoring Agent ručně. |


## Řešení potíží se síťovými požadavky na agenta monitorování <a name="mon-network-req"></a>
Agenti, kteří se připojují ke službě Security Center a registrují se v ní, musí mít přístup k síťovým prostředkům, včetně čísel portů a doménových adres URL.

- U proxy serverů musíte zajistit konfiguraci příslušných prostředků proxy serveru v nastavení agenta. Přečtěte si tento článek, ve kterém se dozvíte další informace o [tom, jak změnit nastavení proxy](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings).
- Pokud používáte k omezení přístupu k internetu bránu firewall, je nutné ji nakonfigurovat tak, aby povolovala přístup k Log Analytics. V nastavení agenta nemusíte nic konfigurovat.

V následující tabulce najdete přehled prostředků potřebných pro komunikaci.

| Prostředek agenta | Porty | Obejít kontrolu protokolu HTTPS |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ano |
| *.oms.opinsights.azure.com | 443 | Ano |
| *.blob.core.windows.net | 443 | Ano |
| *.azure-automation.net | 443 | Ano |

Pokud narazíte na problémy s registrací agenta, přečtěte si článek [Řešení potíží s registrací v Operations Management Suite](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Řešení potíží s tím, že ochrana koncových bodů nefunguje správně

Agent hosta je nadřazený proces pro všechno, co dělá rozšíření [Microsoft Antimalware](../security/azure-security-antimalware.md). Pokud proces agenta hosta selže, může se stát, že selže i Microsoft Antimalware, který je spuštěný jako jeho podřízený proces.  V podobných situacích se doporučuje ověřit následující možnosti:

- Pokud je cílový virtuální počítač založený na vlastní imagi a tvůrce virtuálního počítače nikdy nenainstaloval agent hosta.
- Pokud cílový virtuální počítač místo Windows používá Linux, instalace verze antimalwarového rozšíření pro Windows na linuxovém virtuálním počítači selže. Linuxový agent hosta má specifické požadavky na verzi operačního systému a požadované balíčky. Pokud tyto požadavky nejsou splněny, agent virtuálního počítače také nebude fungovat.
- Pokud se při vytvoření virtuálního počítače použila stará verze agenta hosta. Pokud ano, měli byste vědět, že u některých starých agentů nefunguje automatická aktualizace na novější verzi a to by mohlo vést k tomuto problému. Pokud vytváříte vlastní image, vždy používejte nejnovější verzi agenta hosta.
- Některé softwary pro správu třetích stran mohou zakázat agenta hosta nebo blokovat přístup k určitým umístěním souborů. Pokud máte ve virtuálním počítači nainstalovaný software třetích stran, ujistěte se, že agent je uvedený v seznamu vyloučení.
- Určitá nastavení brány firewall nebo skupiny zabezpečení sítě (NSG) mohou zablokovat síťový provoz do a z agenta hosta.
- Některé seznamy řízení přístupu (ACL) mohou bránit v přístupu k disku.
- Nedostatek místa na disku může blokovat agent hosta a ten nebude fungovat správně.

Ve výchozím nastavení je uživatelské rozhraní rozšíření Microsoft Antimalware zablokované. Další informace o tom, jak ho v případě potřeby povolit, najdete v tématu věnovaném [povolení uživatelského rozhraní rozšíření Microsoft Antimalware ve virtuálních počítačích s Azure Resource Managerem po nasazení](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/).

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Odstraňování potíží s načtením řídicího panelu

Pokud dochází k problémům s načtením řídicího panelu služby Security Center, ujistěte se, že uživatel, který předplatné ke službě Security Center registruje (tj. uživatel, který s tímto předplatným otevřel službu Security Center) a uživatel, který chce zapnout shromažďování dat, mají u daného předplatného roli *Vlastník* nebo *Přispěvatel*. Od této chvíle budou moci i uživatelé, kteří u předplatného mají roli *Čtenář*, zobrazovat řídicí panel, upozornění, doporučení a zásady.

## <a name="contacting-microsoft-support"></a>Kontaktování oddělení podpory společnosti Microsoft
Některé potíže lze identifikovat podle pokynů v tomto článku, některé další jsou také dokumentovány ve veřejném [fóru](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) služby Security Center. Pokud však potřebujete odstraňovat potíže mimo tento rámec, můžete vytvořit novou žádost o podporu prostřednictvím webu **Azure Portal**, jak je znázorněno níže:

![Podpora společnosti Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Další informace najdete v tématech
V tomto dokumentu jste zjistili, jak ve službě Azure Security Center konfigurovat zásady zabezpečení. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu, abyste mohli přejít na Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů
