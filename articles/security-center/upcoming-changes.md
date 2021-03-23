---
title: Důležité změny přicházející do Azure Security Center
description: Nadcházející změny Azure Security Center, na které může být nutné vědět a pro které může být nutné naplánovat
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/18/2021
ms.author: memildin
ms.openlocfilehash: b9a93286b6a546160b6c621d084437f671eab4d3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773568"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

| Plánovaná změna                                                                                                                                                        | Odhadované datum změny |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému.](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | Březen 2021                |
| [Vyřazení 11 výstrah v programu Azure Defender jako vyřazení](#deprecation-of-11-azure-defender-alerts)                                                                                   | Březen 2021                |
| [21 doporučení mezi ovládacími prvky zabezpečení](#21-recommendations-moving-between-security-controls)                                                           | Duben 2021                |
| [Další dvě doporučení pro řízení zabezpečení použít aktualizace systému jsou zastaralá.](#two-further-recommendations-from-apply-system-updates-security-control-being-deprecated)                                                                                         | Duben 2021                |
| [Doporučení od AWS se uvolní pro obecnou dostupnost (GA).](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | Duben 2021                |
| [Vylepšení doporučení klasifikace dat SQL](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Nepoužívá se dvě doporučení pro řízení zabezpečení použít aktualizace systému. 

**Odhadované datum změny:** Březen 2021

Následující dvě doporučení se naplánují jako zastaralá v únoru 2021:

- Počítače **by měly být restartovány, aby bylo možné použít aktualizace systému**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.
- **Agent monitorování by měl být nainstalovaný na vašich počítačích**. Toto doporučení se týká pouze místních počítačů a některé z jeho logiky se přenesou na jiné doporučení **Log Analytics by se měly vyřešit problémy se stavem agenta na vašich počítačích**. To může vést ke mírnému dopadu na vaše zabezpečené skóre.

Doporučujeme, abyste provedli kontrolu průběžného exportu a konfigurací automatizace pracovních postupů, abyste zjistili, jestli jsou tato doporučení zahrnutá v nich. Také by se měly aktualizovat všechny řídicí panely nebo jiné nástroje pro monitorování, které by je mohly používat.

Další informace o těchto doporučeních najdete na [referenční stránce doporučení zabezpečení](recommendations-reference.md).

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
 




### <a name="21-recommendations-moving-between-security-controls"></a>21 doporučení mezi ovládacími prvky zabezpečení 

**Odhadované datum změny:** Duben 2021

Následující doporučení se přesunou na jiný ovládací prvek zabezpečení. Ovládací prvky zabezpečení jsou logické skupiny souvisejících doporučení zabezpečení a odráží vaše zranitelné plochy pro útok. Toto přesunutí zajistí, že každé z těchto doporučení je nejvhodnějším ovládacím prvkem, aby splnila svůj cíl. 

Seznamte se s doporučeními v jednotlivých ovládacích prvcích zabezpečení v ovládacích prvcích zabezpečení a jejich doporučeních.

|Doporučení |Změnit a ovlivnit  |
|---------|---------|
|Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení<br>Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení<br>Chyby zabezpečení v databázích SQL by měly být opraveny novinkou<br>Ohrožení zabezpečení vašich databází SQL na virtuálních počítačích by mělo být opraveno.     |Přesun z nápravy ohrožení zabezpečení (má 6 bodů)<br>náprava konfigurací zabezpečení (v hodnotě 4 bodů).<br>V závislosti na vašem prostředí budou mít tato doporučení slabší dopad na vaše skóre.|
|K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.<br>Proměnné účtu Automation by se měly šifrovat.<br>Zařízení IoT – auditované procesy zastavily odesílání událostí<br>Zařízení IoT – chyba ověřování standardních hodnot operačního systému<br>Zařízení IoT – vyžaduje se upgrade TLS cipher suite<br>Zařízení IoT – otevření portů na zařízení<br>Zařízení IoT – v jednom z řetězů se nacházely opravňující zásady brány firewall.<br>Zařízení IoT – bylo nalezeno opravňující pravidlo brány firewall ve vstupním řetězci.<br>Zařízení IoT – bylo nalezeno opravňující pravidlo brány firewall ve výstupním řetězci.<br>Měly by být povolené diagnostické protokoly v IoT Hub.<br>Zařízení IoT – agent odesílající nevyužité zprávy<br>Zařízení IoT – výchozí zásada filtru IP adres by měla být odepřena<br>Zařízení IoT – velký rozsah IP adres pravidla filtru IP<br>Zařízení IoT – intervaly a velikosti zpráv agenta by měly být upraveny<br>Zařízení IoT – totožné přihlašovací údaje pro ověřování<br>Zařízení IoT – auditované procesy zastavily odesílání událostí<br>Zařízení IoT – konfigurace standardních hodnot operačního systému (OS) by měla být opravena|Přesunutím můžete implementovat osvědčené **postupy zabezpečení**.<br>Pokud se doporučení přesune k řízení zabezpečení osvědčené postupy zabezpečení, které nestojí za žádných bodů, doporučení už nebude mít vliv na vaše zabezpečené skóre.|
|||


### <a name="two-further-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Další dvě doporučení pro řízení zabezpečení použít aktualizace systému jsou zastaralá.

**Odhadované datum změny:** Duben 2021

Následující dvě doporučení jsou zastaralá:

- **Verze operačního systému by se měla aktualizovat pro vaše role cloudové služby** – ve výchozím nastavení Azure pravidelně aktualizuje hostovaný operační systém na nejnovější podporovanou image v rámci řady operačních systémů, kterou jste zadali v konfiguraci služby (. cscfg), jako je například Windows Server 2016.
- **Kubernetes služby by měly být upgradovány na nezranitelnou Kubernetes verzi** – hodnocení tohoto doporučení nejsou v rozsahu, ve kterém bychom chtěli. Aktuální verze tohoto doporučení bude nakonec nahrazena rozšířenou verzí, která je lépe zarovnaná s potřebou zabezpečení našeho zákazníka.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Doporučení od AWS se uvolní pro obecnou dostupnost (GA).

**Odhadované datum změny:** Duben 2021

Azure Security Center chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Doporučení přicházející z centra zabezpečení AWS byla ve verzi Preview, protože cloudové konektory byly zavedeny. Doporučení označená jako ve **verzi Preview** nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre, ale je potřeba je pořád opravit, kdykoli to bude možné, takže až skončí období Preview, přispěje k vašemu skóre.

V této změně se dvě sady doporučení AWS přesunou na GA:

- [PCI DSS ovládací prvky centra zabezpečení](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Řízení srovnávacích standardů AWS Foundation centra zabezpečení](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Pokud jsou to GA a posouzení se spouští na vašich AWSch prostředcích, výsledky budou mít vliv na vaše kombinované zabezpečené skóre pro všechny vaše hybridní a hybridní cloudové prostředky. 



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Vylepšení doporučení klasifikace dat SQL

**Odhadované datum změny:** Q2 2021

Doporučení **citlivá data v databázích SQL by měla být klasifikována** v části použít bezpečnostní ovládací prvek zabezpečení **klasifikace dat** , bude nahrazena novou verzí, která je lépe zarovnána s strategií klasifikace dat společnosti Microsoft. V důsledku toho se změní i ID doporučení (aktuálně se jedná o b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).