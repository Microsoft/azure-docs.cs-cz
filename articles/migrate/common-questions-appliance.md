---
title: Nejčastější dotazy k Azure Migrate zařízením
description: Získejte odpovědi na běžné otázky týkající se zařízení Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 529ead1baa769ee3e71f6fcf77ef7e020ed196a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81529684"
---
# <a name="azure-migrate-appliance-common-questions"></a>Zařízení Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se zařízení Azure Migrate. Pokud máte další otázky, ověřte tyto prostředky:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- Dotazy týkající [se vizualizace zjišťování, hodnocení a závislostí](common-questions-discovery-assessment.md)
- Dotazy týkající se [migrace serveru](common-questions-server-migration.md)
- Získání otázek zodpovězených ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Co je zařízení Azure Migrate?

Zařízení Azure Migrate je odlehčené zařízení, které Azure Migrate: Nástroj pro vyhodnocení serveru používá ke zjišťování a vyhodnocení místních serverů. Azure Migrate: Nástroj pro migraci serveru používá také zařízení pro migraci místních virtuálních počítačů VMware bez agenta.

Zde jsou další informace o zařízení Azure Migrate:

- Zařízení je nasazené místně jako virtuální počítač nebo fyzický počítač.
- Zařízení zjišťuje místní počítače a průběžně odesílá metadata a data o výkonu počítače do Azure Migrate.
- Zjišťování zařízení je bez agenta. Na zjištěných počítačích není nic nainstalované.

[Přečtěte si další informace](migrate-appliance.md) o zařízení.

## <a name="how-can-i-deploy-the-appliance"></a>Jak můžu zařízení nasadit?

Zařízení se dá nasadit takto:

- Použití šablony pro virtuální počítače VMware a virtuální počítače Hyper-V (šablona vajíček pro VMware nebo VHD pro Hyper-V).
- Pokud nechcete používat šablonu nebo jste v Azure Government, můžete zařízení nasadit pro VMware nebo Hyper-V pomocí skriptu PowerShellu.
- U fyzických serverů vždy nasadíte zařízení pomocí skriptu.


## <a name="how-does-the-appliance-connect-to-azure"></a>Jak se zařízení připojuje k Azure?

Zařízení se může připojit přes Internet nebo pomocí Azure ExpressRoute s veřejným partnerským vztahem/Microsoftu.

## <a name="does-appliance-analysis-affect-performance"></a>Má analýza zařízení vliv na výkon?

Zařízení Azure Migrate profilování místních počítačů neustále měří údaje o výkonu. Tato profilace má téměř žádný vliv na profilování počítačů.

## <a name="can-i-harden-the-appliance-vm"></a>Můžu posílit svůj virtuální počítač zařízení?

Když použijete staženou šablonu k vytvoření virtuálního počítače zařízení, můžete do šablony přidat komponenty (například antivirový program), pokud necháte zachovat pravidla komunikace a brány firewall požadovaná pro Azure Migrate zařízení.

## <a name="what-network-connectivity-is-required"></a>Jaké síťové připojení je potřeba?


Zařízení potřebuje přístup k adresám URL Azure. [Zkontrolujte](migrate-appliance.md#url-access) seznam adres URL.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data shromažďuje zařízení?

Informace o datech, která Azure Migrate zařízení shromažďuje na virtuálních počítačích, najdete v následujících článcích:

- **Virtuální počítač VMware**: [Projděte si](migrate-appliance.md#collected-data---vmware) shromážděná data. [
- **Virtuální počítač Hyper-V**: [Projděte si](migrate-appliance.md#collected-data---hyper-v) shromážděná data.

## <a name="how-is-data-stored"></a>Jak se data ukládají?

Data shromažďovaná zařízením Azure Migrate se ukládají do umístění Azure, ve kterém jste vytvořili projekt Azure Migrate.

Zde jsou další informace o tom, jak jsou data uložená:

- Shromážděná data jsou bezpečně uložená v CosmosDB v předplatném Microsoftu. Data jsou odstraněna při odstranění Azure Migrate projektu. Úložiště zpracovává Azure Migrate. Nemůžete konkrétně zvolit účet úložiště pro shromážděná data.
- Pokud používáte [vizualizaci závislostí](concepts-dependency-visualization.md), shromážděná data se uloží do USA v pracovním prostoru Azure Log Analytics, který jste vytvořili v předplatném Azure. Data se odstraní při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Kolik dat se nahrává během nepřetržitého profilace?

Objem dat odesílaných do Azure Migrate závisí na několika parametrech. Například Azure Migrate projekt, který má 10 počítačů (každý s jedním diskem a jednou síťovou kartou), odesílá přibližně 50 MB dat za den. Tato hodnota je přibližná; Skutečná hodnota se liší v závislosti na počtu datových bodů pro disky a síťové karty. Pokud se zvýší počet počítačů, disků nebo síťových karet, navýšení přenášených dat je nelineární.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Jsou data v klidovém stavu zašifrovaná a přenosná?

Ano, pro:

- Metadata se bezpečně odesílají do služby Azure Migrate přes Internet prostřednictvím protokolu HTTPS.
- Metadata se ukládají do databáze [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) a do služby [Azure Blob Storage](../storage/common/storage-service-encryption.md) v předplatném Microsoftu. Metadata jsou v klidovém stavu zašifrovaná pro úložiště.
- Data pro analýzu závislostí se také šifrují při přenosu (zabezpečeným protokolem HTTPS). Je uložený v pracovním prostoru Log Analytics v rámci vašeho předplatného. Data jsou v klidovém stavu zašifrovaná pro analýzu závislostí.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Jak se zařízení připojuje k vCenter Server?

Tento postup popisuje, jak se zařízení připojuje k VMware vCenter Server:

1. Zařízení se připojí k vCenter Server (port 443) pomocí přihlašovacích údajů, které jste zadali při nastavení zařízení.
2. Zařízení používá VMware PowerCLI k dotazování vCenter Server ke shromažďování metadat o virtuálních počítačích, které jsou spravované pomocí vCenter Server.
3. Zařízení shromažďuje konfigurační data o virtuálních počítačích (jádrech, paměti, discích, síťových rozhraních) a historii výkonu každého virtuálního počítače za minulý měsíc.
4. Shromážděná metadata se odesílají do Azure Migrate: Nástroj pro vyhodnocení serveru (přes Internet prostřednictvím protokolu HTTPS) pro posouzení.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Může se zařízení Azure Migrate připojit k více serverům vCenter?

Ne. Mezi [zařízením Azure Migrate](migrate-appliance.md) a vCenter Server existuje mapování 1:1. Chcete-li zjistit virtuální počítače ve více instancích vCenter Server, je nutné nasadit více zařízení. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Může Azure Migrate projekt mít více zařízení?
K projektu může být připojeno více zařízení. Zařízení je však možné přidružit pouze k jednomu projektu. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Může se zařízení Azure Migrate/zařízení replikace připojit ke stejnému vCenter?
Ano. Do stejného serveru vCenter můžete přidat Azure Migrate zařízení (používá se pro vyhodnocení a migraci VMware bez agentů) a zařízení replikace (používané pro migraci virtuálních počítačů VMware na základě agenta).


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Kolik virtuálních počítačů nebo serverů můžu zjistit pomocí zařízení?

Můžete zjistit až 10 000 virtuálních počítačů VMware, až 5 000 virtuálních počítačů Hyper-V, a až 250 fyzické servery s jedním zařízením. Pokud máte ve svém místním prostředí více počítačů, přečtěte si o [škálování posouzení technologie Hyper-V](scale-hyper-v-assessment.md), [škálování posouzení VMware](scale-vmware-assessment.md)a [škálování posouzení fyzického serveru](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Můžu odstranit zařízení?

Odstranění zařízení z projektu v současné době se nepodporuje.

Jediným způsobem, jak zařízení odstranit, je odstranit skupinu prostředků, která obsahuje Azure Migrate projektu, který je přidružený k danému zařízení.

Odstraněním skupiny prostředků se ale odstraní taky další registrovaná zařízení, zjištěná inventarizace, posouzení a všechny ostatní součásti Azure ve skupině prostředků, která je přidružená k projektu.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Můžu zařízení používat s jiným předplatným nebo projektem?

Po použití zařízení k zahájení zjišťování nemůžete znovu nakonfigurovat zařízení tak, aby se používalo s jiným předplatným Azure, a nemůžete ho použít v jiném Azure Migrateovém projektu. Virtuální počítače nemůžete najít ani na jiné instanci vCenter Server. Nastavte nové zařízení pro tyto úlohy.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Můžu zařízení nastavit na virtuálním počítači Azure?

Ne. V tuto chvíli není tato možnost podporovaná. 

## <a name="can-i-discover-on-an-esxi-host"></a>Můžu na hostiteli ESXi zjistit?

Ne. Pokud chcete zjistit virtuální počítače VMware, musíte mít vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Návody aktualizovat zařízení?

Ve výchozím nastavení se zařízení a jeho instalované agenti aktualizují automaticky. Zařízení vyhledává aktualizace každých 24 hodin. Aktualizace, které selžou, se zopakují. 

Pomocí těchto automatických aktualizací se aktualizují jenom zařízení a agenti zařízení. Operační systém se Azure Migrate automatické aktualizace neaktualizuje. Pomocí aktualizací Windows můžete udržovat operační systém v aktuálním stavu.

## <a name="can-i-check-agent-health"></a>Můžu kontrolovat stav agenta?

Ano. Na portálu navštivte stránku **Stav agenta** pro Azure Migrate: Server Assessment nebo Azure Migrate: Nástroj pro migraci serveru. Tady můžete kontrolovat stav připojení mezi Azure a agenty zjišťování a vyhodnocení na zařízení.

## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
