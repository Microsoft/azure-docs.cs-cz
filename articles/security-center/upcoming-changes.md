---
title: Důležité změny přicházející do Azure Security Center
description: Nadcházející změny Azure Security Center, na které může být nutné vědět a pro které může být nutné naplánovat
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 49141f7f11c0e8ead090459238e15b56f57b990b
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633712"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

- [Doporučení od AWS se uvolní pro obecnou dostupnost (GA).](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému.](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Vylepšení doporučení klasifikace dat SQL](#enhancements-to-sql-data-classification-recommendation)
- [Vyřazení 11 výstrah v programu Azure Defender jako vyřazení](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Doporučení od AWS se uvolní pro obecnou dostupnost (GA).

**Odhadované datum změny:** Duben 2021

Azure Security Center chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Doporučení přicházející z centra zabezpečení AWS byla ve verzi Preview, protože cloudové konektory byly zavedeny. Doporučení označená jako ve **verzi Preview** nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre, ale je potřeba je pořád opravit, kdykoli to bude možné, takže až skončí období Preview, přispěje k vašemu skóre.

V této změně se dvě sady doporučení AWS přesunou na GA:

- [PCI DSS ovládací prvky centra zabezpečení](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Řízení srovnávacích standardů AWS Foundation centra zabezpečení](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Pokud jsou to GA a posouzení se spouští na vašich AWSch prostředcích, výsledky budou mít vliv na vaše kombinované zabezpečené skóre pro všechny vaše hybridní a hybridní cloudové prostředky. 


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému. 

**Odhadované datum změny:** Březen 2021

Následující dvě doporučení se naplánují jako zastaralá v únoru 2021:

- Počítače **by měly být restartovány, aby bylo možné použít aktualizace systému**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.
- **Agent monitorování by měl být nainstalovaný na vašich počítačích**. Toto doporučení se týká pouze místních počítačů a některé z jeho logiky se přenesou na jiné doporučení **Log Analytics by se měly vyřešit problémy se stavem agenta na vašich počítačích**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.

Doporučujeme, abyste provedli kontrolu průběžného exportu a konfigurací automatizace pracovních postupů, abyste zjistili, jestli jsou tato doporučení zahrnutá v nich. Také by se měly aktualizovat všechny řídicí panely nebo jiné nástroje pro monitorování, které by je mohly používat.

Další informace o těchto doporučeních najdete na [referenční stránce doporučení zabezpečení](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Vylepšení doporučení klasifikace dat SQL

**Odhadované datum změny:** Q2 2021

Doporučení **citlivá data v databázích SQL by měla být klasifikována** v části použít bezpečnostní ovládací prvek zabezpečení **klasifikace dat** , bude nahrazena novou verzí, která je lépe zarovnána s strategií klasifikace dat společnosti Microsoft. V důsledku toho se změní i ID doporučení (aktuálně se jedná o b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="deprecation-of-11-azure-defender-alerts"></a>Vyřazení 11 výstrah v programu Azure Defender jako vyřazení

**Odhadované datum změny:** Březen 2021

Příští měsíc budou jedenácté výstrahy Azure Defenderu uvedené dál zastaralé.

- Nové výstrahy nahradí tyto dvě upozornění a budou poskytovat lepší pokrytí:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW – zjistilo se spuštění funkce pro mikroshluking Toolkit Get-AzureDomainInfo. |
    | ARM_MicroBurstRunbook    | PREVIEW – zjistilo se spuštění funkce pro mikroshluking Toolkit Get-AzurePasswords.  |
    |                          |                                                                          |

- Tyto devět výstrah se týkají konektoru Azure Active Directory Identity Protection, který již byl zastaralý:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Neznámé vlastnosti přihlášení |
    | AnonymousLogin      | Anonymní IP adresa          |
    | InfectedDeviceLogin | Propojená IP adresa pro malware     |
    | ImpossibleTravel    | Neobvyklá cesta               |
    | MaliciousIP         | Škodlivá IP adresa          |
    | LeakedCredentials   | Uniklé přihlašovací údaje            |
    | PasswordSpray       | Sprej hesla                |
    | LeakedCredentials   | Analýza hrozeb v Azure AD  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).
