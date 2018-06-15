---
title: Správa doporučení zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument vás provede jak doporučení v Azure Security Center pomáhá zůstat souladu se zásadami zabezpečení a ochraně vašich prostředků Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 72070f46309adb526901192752fe421a3846398b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364246"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Správa doporučení zabezpečení v Azure Security Center
Tento dokument vás provede procesem jak používat doporučení v Azure Security Center k ochraně vašich prostředků Azure.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není to podrobný průvodce.
>
>

## <a name="what-are-security-recommendations"></a>Jaké jsou doporučení zabezpečení?
Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

## <a name="implementing-security-recommendations"></a>Implementace doporučení zabezpečení
### <a name="set-recommendations"></a>Sadu doporučení
V [nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md), Naučte se:

* Konfigurovat zásady zabezpečení.
* Shromažďování dat zapněte.
* Vyberte, které doporučení zobrazíte v rámci svých zásad zabezpečení.

Aktuální zásady doporučení center kolem aktualizací systému, standardních pravidel, antimalwarových programů, [skupin zabezpečení sítě](../virtual-network/security-overview.md) na podsítí a síťových rozhraní, auditování databáze SQL, SQL database transparentní šifrování dat, a webové aplikace brány firewall.  [Nastavení zásad zabezpečení](security-center-policies.md) obsahuje popis jednotlivých možností doporučení.

### <a name="monitor-recommendations"></a>Monitorování doporučení
Po nastavení zásad zabezpečení bude Security Center analyzovat stav zabezpečení vašich prostředků Azure za účelem identifikace potenciálních ohrožení zabezpečení. **Doporučení** dlaždici v části **přehled** umožňuje zjistit celkový počet doporučení identifikovaný Security Center.

![Dlaždice doporučení][1]

Pokud chcete zobrazit podrobnosti o každé doporučení, vyberte **doporučení dlaždici** pod **přehled**. **Doporučení** otevře.

![Filtrovat doporučení][2]

Doporučení jsou zobrazena ve formátu tabulky, kde každý řádek představuje jedno konkrétní doporučení. Sloupce v této tabulce jsou:

* **Popis**: vysvětluje doporučení a co je třeba provést k vyřešení ho.
* **PROSTŘEDEK**: jsou uvedeny prostředky, u kterých bude použito toto doporučení.
* **Stav**: popisuje aktuální stav doporučení:
  * **Otevřete**: doporučení dosud nebylo řešeno.
  * **V průběhu**: doporučení se aktuálně na prostředky a není třeba žádné akce.
  * **Vyřešit**: doporučení již byla dokončena (v tomto případě řádku je zobrazena šedě).
* **SEVERITY** (Závažnost): Popisuje závažnost tohoto konkrétního doporučení:
  * **Vysoká**: ohrožení zabezpečení existuje u významného prostředku (například aplikace, virtuální počítač nebo skupinu zabezpečení sítě) a vyžaduje pozornost.
  * **Střední**: byla zjištěna chyba zabezpečení a nekritické nebo další kroky jsou požadovány k jeho odstranění nebo k dokončení procesu.
  * **Nízká**: obsahuje chybu, mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení, nejsou přítomny nízkou doporučení, ale můžete filtrovat podle nízkou doporučení, pokud chcete, aby byla zobrazena.)

Použijte v následující tabulce vám pomohou pochopit dostupné doporučení a co každé z nich dělá Pokud použijete ho jako odkaz.

> [!NOTE]
> Můžete zjistit [classic a modelech nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
>
>

| Doporučení | Popis |
| --- | --- |
| [Povolení shromažďování dat pro předplatná](security-center-enable-data-collection.md) |Doporučuje zapnout shromažďování dat v zásadě zabezpečení pro každou z vašich předplatných a všechny virtuální počítače Azure (VM) a počítače mimo Azure. |
| [Náprava konfigurací zabezpečení](security-center-remediate-os-vulnerabilities.md) |Doporučuje zarovnat vaše konfigurace operačního systému s pravidla zabezpečení doporučená konfigurace, například, zakázat ukládání hesel. |
| [Instalace aktualizací systému](security-center-apply-system-updates.md) |Doporučuje nasazení chybí systému zabezpečení a důležité aktualizace pro Windows a virtuální počítače s Linuxem a počítače. |
| [Použít pouze v době provedená sítě řízení přístupu](security-center-just-in-time.md) | Doporučuje se použít jenom v přístup k časovému virtuálních počítačů. Právě v čase je funkce ve verzi preview a je k dispozici ve standardní vrstvě služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). |
| [Restartování po aktualizacích systému](security-center-apply-system-updates.md#reboot-after-system-updates) |Doporučuje, abyste restartovali virtuální počítač k dokončení procesu instalace aktualizací systému. |
| [Přidání brány firewall webových aplikací](security-center-add-web-application-firewall.md) |Doporučuje, která můžete nasadit brány firewall webových aplikací (firewall webových aplikací) pro koncových bodů webové. Doporučení firewall webových aplikací je zobrazený pro všechny veřejné přístupných IP adresy (IP úrovni Instance nebo IP skupinu s vyrovnáváním zatížení), skupinu zabezpečení sítě spojenou s otevřete příchozí webovými porty (80,443). </br>Security Center doporučuje zřízení firewall webových aplikací, které pomáhají bránit proti útokům na cílení na vaše webové aplikace na virtuální počítače a služby App Service Environment. Je aplikaci služby prostředí (App Service Environment) [Premium](https://azure.microsoft.com/pricing/details/app-service/) služby možnost plánu služby Azure App Service, která poskytuje plně izolovaném a vyhrazeném prostředí pro zabezpečené spouštění aplikací Azure App Service. Další informace o App Service Environment, najdete v článku [dokumentace k aplikaci služby prostředí](../app-service/environment/intro.md).</br>Přidáním těchto aplikací na vaše stávající nasazení firewall webových aplikací můžete chránit několika webových aplikací ve službě Security Center. |
| [Finalizace ochrany aplikací](security-center-add-web-application-firewall.md#finalize-application-protection) |K dokončení konfigurace firewall webových aplikací, musí být přenos přesměruje do zařízení firewall webových aplikací. Následující toto doporučení dokončení změny potřebné instalační. |
| [Přidání brány firewall příští generace](security-center-add-next-generation-firewall.md) |Doporučuje přidat brány Firewall pro další generace (NGFW) z partnera společnosti Microsoft pro zvýšení ochrany vaší zabezpečení. |
| [Směrování provozu jenom přes NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Doporučuje konfiguraci pravidla zabezpečení skupiny (NSG) sítě, které vynutit příchozí provoz do virtuálního počítače prostřednictvím vaší NGFW. |
| [Instalace Endpoint Protection](security-center-install-endpoint-protection.md) |Doporučuje, abyste do virtuálních počítačů nainstalovali antimalwarové programy (platí pouze pro virtuální počítače s Windows). |
| [Povolení skupin zabezpečení sítě pro podsítě nebo virtuální počítače](security-center-enable-network-security-groups.md) |Doporučuje, abyste povolili skupiny Nsg na podsítě nebo virtuálních počítačů. |
| [Omezení přístupu prostřednictvím internetové koncový bod](security-center-restrict-access-through-internet-facing-endpoints.md) |Doporučuje konfigurace pravidla pro příchozí provoz pro skupiny Nsg. |
| [Povolení auditování a detekce hrozeb na SQL serverech](security-center-enable-auditing-on-sql-servers.md) |Doporučuje zapnout auditování a zjišťování hrozeb pro servery Azure SQL. (Jenom služba azure SQL. Neobsahuje SQL běžících na virtuálních počítačích.) |
| [Povolení auditování a detekce hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje zapnout auditování a zjišťování hrozeb pro databáze Azure SQL. (Jenom služba azure SQL. Neobsahuje SQL běžících na virtuálních počítačích.) |
| [Povolit transparentní šifrování dat v databázích SQL](security-center-enable-transparent-data-encryption.md) |Doporučuje se, že povolíte šifrování pro databáze SQL. (Služba azure SQL pouze.) |
| [Povolení agenta virtuálního počítače](security-center-enable-vm-agent.md) |Umožňuje vám zobrazit, které virtuální počítače vyžadují agenta virtuálního počítače. Agent virtuálního počítače musí být nainstalován na virtuálních počítačích na opravu zřizování, kontrolu, kontrolu směrného plánu a antimalwarových programů. Agent virtuálního počítače je ve výchozím nastavení nainstalován na virtuálních počítačích nasazených z Azure Marketplace. V článku [Agenti a rozšíření virtuálních počítačů – Část 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) najdete informace o tom, jak agenta virtuálního počítače nainstalovat. |
| [Použití šifrování disku](security-center-apply-disk-encryption.md) |Doporučuje, abyste disky svých virtuálních počítačů zašifrovali pomocí služby Azure Disk Encryption (platí pro virtuální počítače s Windows a Linuxem). Na virtuálním počítači se doporučuje šifrování svazku operačního systému i svazku s daty. |
| [Poskytnutí podrobností kontaktů zabezpečení](security-center-provide-security-contact-details.md) |Doporučuje se, že zadáte zabezpečení kontaktní informace pro každé z vašich předplatných. Kontaktní informace je e-mailovou adresu a telefonní číslo. Tyto informace slouží k vás kontaktovat, pokud náš tým zabezpečení zjistí, že jsou ohrožení zabezpečení vašich prostředků. |
| [Aktualizace verze operačního systému](security-center-update-os-version.md) |Doporučuje aktualizovat na verzi operačního systému (OS) pro cloudové služby na nejnovější dostupnou verzi pro operačních systémů.  Další informace o službách Cloud Services najdete v tématu [cloudové služby přehled](../cloud-services/cloud-services-choose-me.md). |
| [Není nainstalováno posouzení ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md) |Doporučuje, abyste na vašem virtuálním počítači nainstalovali řešení posouzení ohrožení zabezpečení. |
| [Náprava ohrožení zabezpečení](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umožňuje vám zobrazit ohrožení zabezpečení systému a aplikací zjištěná řešením posouzení ohrožení zabezpečení nainstalovaným na vašem virtuálním počítači. |
| [Povolit šifrování pro účet úložiště Azure](security-center-enable-encryption-for-storage-account.md) | Doporučuje povolit šifrování služby úložiště Azure pro data v klidovém stavu. Šifrování služby úložiště (SSE) funguje tak, že šifrování dat při je zapsán do úložiště Azure a dešifruje před načtení. SSE aktuálně nejsou k dispozici pouze pro službu Azure Blob lze použít pro objekty BLOB bloku, objektů BLOB stránky a doplňovacích objektů BLOB. Další informace najdete v tématu [šifrování služby úložiště pro data v klidovém stavu](../storage/common/storage-service-encryption.md).</br>SSE je podporována pouze na účty úložiště Resource Manager. |

Můžete filtrovat a zavřít doporučení.

1. Vyberte **filtru** na **doporučení** okno. **Filtru** otevře se okno a vybrat stav a závažnost hodnoty, které chcete vidět.

2. Pokud zjistíte, že doporučení se nedá použít, můžete zrušit doporučení a pak ji odfiltrovat ze zobrazení. Existují dva způsoby zamítnutí doporučení. Jedním ze způsobů je klikněte pravým tlačítkem na položku a pak vyberte **přeskočení**. Druhá je pozastavte ukazatel myši nad položku, klikněte na tlačítko se třemi tečkami, které se zobrazí napravo a potom vyberte **přeskočení**. Kliknutím můžete zobrazit dismissed doporučení **filtru**a potom vyberete **zamítnuto**.

    ![Zavření doporučení][3]

### <a name="apply-recommendations"></a>Použít doporučení
Po kontrole všech doporučení, rozhodněte, který jeden byste měli použít první. Doporučujeme používat hodnocení závažnosti, jako parametr hlavní k vyhodnocení, která doporučení by měl použít první.

V tabulce předchozí doporučení vyberte doporučení a provede ji jako příklad tom, jak používat doporučení.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se seznámili s doporučení zabezpečení v Centru zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
