---
title: Nejčastější dotazy k Azure Migrate zařízením
description: Získejte odpovědi na běžné otázky týkající se zařízení Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 5a050d9aab9e8665c6048391488e57c9b4af10a5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043061"
---
# <a name="azure-migrate-appliance-common-questions"></a>Zařízení Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se zařízení Azure Migrate. Pokud máte další otázky, ověřte tyto prostředky:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- Dotazy týkající [se vizualizace zjišťování, hodnocení a závislostí](common-questions-discovery-assessment.md)
- Dotazy týkající se [migrace serveru](common-questions-server-migration.md)
- Získání otázek zodpovězených ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Co je zařízení Azure Migrate?

Zařízení Azure Migrate je odlehčené zařízení, které Azure Migrate: Nástroj pro vyhodnocení serveru používá ke zjišťování a vyhodnocení fyzických nebo virtuálních serverů z místního prostředí nebo jakéhokoli cloudu. Azure Migrate: Nástroj pro migraci serveru používá také zařízení pro migraci místních virtuálních počítačů VMware bez agenta.

Zde jsou další informace o zařízení Azure Migrate:

- Zařízení je nasazené místně jako virtuální počítač nebo fyzický počítač.
- Zařízení zjišťuje místní počítače a průběžně odesílá metadata a data o výkonu počítače do Azure Migrate.
- Zjišťování zařízení je bez agenta. Na zjištěných počítačích není nic nainstalované.

[Přečtěte si další informace](migrate-appliance.md) o zařízení.

## <a name="how-can-i-deploy-the-appliance"></a>Jak můžu zařízení nasadit?

Zařízení je možné nasadit pomocí několika metod:

- Zařízení se dá nasadit pomocí šablony pro servery, které běží v prostředí VMware nebo Hyper-V ([Šablona vajíček pro VMware](how-to-set-up-appliance-vmware.md) nebo [VHD pro Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Pokud nechcete používat šablonu, můžete zařízení nasadit pro prostředí VMware nebo Hyper-V pomocí [skriptu instalačního programu PowerShell](deploy-appliance-script.md).
- V Azure Government byste zařízení měli nasadit pomocí skriptu instalačního programu PowerShell. [Tady](deploy-appliance-script-government.md)najdete postup nasazení.
- U fyzických nebo virtualizovaných serverů v místním prostředí nebo v jakémkoli jiném cloudu vždy nasadíte zařízení pomocí skriptu instalačního programu PowerShell. [Tady](how-to-set-up-appliance-physical.md)najdete postup nasazení.

## <a name="how-does-the-appliance-connect-to-azure"></a>Jak se zařízení připojuje k Azure?

Zařízení se může připojit přes Internet nebo pomocí Azure ExpressRoute. 

- Ujistěte se, že se zařízení může připojit k těmto [adresám URL Azure](./migrate-appliance.md#url-access). 
- ExpressRoute můžete použít s partnerským vztahem Microsoftu. Veřejný partnerský vztah je zastaralý a není k dispozici pro nové okruhy ExpressRoute.
- Pouze privátní partnerské vztahy se nepodporují.


## <a name="does-appliance-analysis-affect-performance"></a>Má analýza zařízení vliv na výkon?

Zařízení Azure Migrate profilování místních počítačů neustále měří údaje o výkonu. Tato profilace má téměř žádný vliv na profilování počítačů.

## <a name="can-i-harden-the-appliance-vm"></a>Můžu posílit svůj virtuální počítač zařízení?

Když použijete staženou šablonu k vytvoření virtuálního počítače zařízení, můžete do šablony přidat komponenty (například antivirový program), pokud necháte zachovat pravidla komunikace a brány firewall požadovaná pro Azure Migrate zařízení.

## <a name="what-network-connectivity-is-required"></a>Jaké síťové připojení je potřeba?

Zařízení potřebuje přístup k adresám URL Azure. [Zkontrolujte](migrate-appliance.md#url-access) seznam adres URL.

## <a name="what-data-does-the-appliance-collect"></a>Jaká data shromažďuje zařízení?

Informace o datech, která Azure Migrate zařízení shromažďuje na virtuálních počítačích, najdete v následujících článcích:

- **Virtuální počítač VMware**: [Projděte si](migrate-appliance.md#collected-data---vmware) shromážděná data.
- **Virtuální počítač Hyper-V**: [Projděte si](migrate-appliance.md#collected-data---hyper-v) shromážděná data.
- **Fyzické nebo virtuální servery**:[Prohlédněte si](migrate-appliance.md#collected-data---physical) shromážděná data.

## <a name="how-is-data-stored"></a>Jak se data ukládají?

Data shromažďovaná zařízením Azure Migrate se ukládají do umístění Azure, ve kterém jste vytvořili projekt Azure Migrate.

Zde jsou další informace o tom, jak jsou data uložená:

- Shromážděná data jsou bezpečně uložená v CosmosDB v předplatném Microsoftu. Data jsou odstraněna při odstranění Azure Migrate projektu. Úložiště zpracovává Azure Migrate. Nemůžete konkrétně zvolit účet úložiště pro shromážděná data.
- Pokud používáte [vizualizaci závislostí](concepts-dependency-visualization.md), shromažďovaná data se ukládají v pracovním prostoru Azure Log Analytics vytvořeném ve vašem předplatném Azure. Data se odstraní při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného. 

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

No. Mezi [zařízením Azure Migrate](migrate-appliance.md) a vCenter Server existuje mapování 1:1. Chcete-li zjistit virtuální počítače ve více instancích vCenter Server, je nutné nasadit více zařízení. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Může Azure Migrate projekt mít více zařízení?

K projektu může být zaregistrováno více zařízení. Jedno zařízení lze však zaregistrovat pouze v jednom projektu.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Může se zařízení Azure Migrate/zařízení replikace připojit ke stejnému vCenter?

Ano. Do stejného serveru vCenter můžete přidat Azure Migrate zařízení (používá se pro vyhodnocení a migraci VMware bez agentů) a zařízení replikace (používané pro migraci virtuálních počítačů VMware na základě agenta). Ujistěte se ale, že nenastavujete obě zařízení na jednom virtuálním počítači a že se v tuto chvíli nepodporuje.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Kolik virtuálních počítačů nebo serverů můžu zjistit pomocí zařízení?

Můžete zjistit až 10 000 virtuálních počítačů VMware, až 5 000 virtuálních počítačů Hyper-V, a až 1000 fyzické servery s jedním zařízením. Pokud máte ve svém místním prostředí více počítačů, přečtěte si o [škálování posouzení technologie Hyper-V](scale-hyper-v-assessment.md), [škálování posouzení VMware](scale-vmware-assessment.md)a [škálování posouzení fyzického serveru](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Můžu odstranit zařízení?

Odstranění zařízení z projektu v současné době se nepodporuje.

Jediným způsobem, jak zařízení odstranit, je odstranit skupinu prostředků, která obsahuje Azure Migrate projektu, který je přidružený k danému zařízení.

Odstraněním skupiny prostředků se ale odstraní taky další registrovaná zařízení, zjištěná inventarizace, posouzení a všechny ostatní součásti Azure ve skupině prostředků, která je přidružená k projektu.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Můžu zařízení používat s jiným předplatným nebo projektem?

Pokud chcete zařízení používat s jiným předplatným nebo projektem, budete muset znovu nakonfigurovat stávající zařízení spuštěním skriptu instalačního programu PowerShellu pro konkrétní scénář (VMware/Hyper-V/fyzický) na počítači zařízení. Skript vyčistí existující součásti a nastavení zařízení, aby se nasadilo nové zařízení. Než začnete používat nově nasazeného Správce konfigurace zařízení, nezapomeňte prosím vymazat mezipaměť prohlížeče.

Také nelze znovu použít existující klíč projektu Azure Migrate na znovu nakonfigurovaném zařízení. Abyste dokončili registraci zařízení, ujistěte se, že jste vygenerovali nový klíč z požadovaného předplatného nebo projektu.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Můžu zařízení nastavit na virtuálním počítači Azure?

No. V tuto chvíli není tato možnost podporovaná.

## <a name="can-i-discover-on-an-esxi-host"></a>Můžu na hostiteli ESXi zjistit?

No. Pokud chcete zjistit virtuální počítače VMware, musíte mít vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Návody aktualizovat zařízení?

Ve výchozím nastavení se zařízení a jeho instalované agenti aktualizují automaticky. Zařízení vyhledává aktualizace každých 24 hodin. Aktualizace, které selžou, se zopakují. 

Pomocí těchto automatických aktualizací se aktualizují jenom zařízení a agenti zařízení. Operační systém se Azure Migrate automatické aktualizace neaktualizuje. Pomocí aktualizací Windows můžete udržovat operační systém v aktuálním stavu.

## <a name="can-i-check-agent-health"></a>Můžu kontrolovat stav agenta?

Ano. Na portálu navštivte stránku **Stav agenta** pro Azure Migrate: Server Assessment nebo Azure Migrate: Nástroj pro migraci serveru. Tady můžete kontrolovat stav připojení mezi Azure a agenty zjišťování a vyhodnocení na zařízení.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Můžu na zařízení VMware přidat víc přihlašovacích údajů k serveru?

Ano, teď podporujeme více přihlašovacích údajů serveru, aby bylo možné provádět inventář softwaru (zjišťování nainstalovaných aplikací), analýzu závislostí bez agentů a zjišťování SQL Server instancí a databází. [Přečtěte si další informace](tutorial-discover-vmware.md#provide-server-credentials) o tom, jak zadat přihlašovací údaje pro Správce konfigurace zařízení.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Jaký typ přihlašovacích údajů serveru můžu přidat na zařízení VMware?
V nástroji Configuration Manager pro zařízení můžete zadat přihlašovací údaje doména/Windows (ne-doména)/Linux (nedoménová)/SQL Server. [Přečtěte si další informace](add-server-credentials.md) o tom, jak zadat přihlašovací údaje a jak je zpracovat.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Jaký typ vlastností připojení SQL Server podporuje Azure Migrate pro zjišťování SQL?
Azure Migrate zašifruje komunikaci mezi Azure Migrate zařízením a instancemi SQL Server zdrojového kódu (s vlastností šifrovat připojení nastavenou na hodnotu TRUE). Tato připojení jsou šifrovaná pomocí [TrustServerCertificate](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (nastavená na hodnotu true); Transportní vrstva bude používat protokol SSL k šifrování kanálu a obejít řetěz certifikátů k ověření vztahu důvěryhodnosti. Server zařízení musí být nastavený tak, aby [důvěřoval kořenové autoritě certifikátu](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Pokud se při spuštění na serveru nezřídí žádný certifikát, SQL Server vygeneruje certifikát podepsaný svým držitelem, který se používá k šifrování přihlašovacích paketů. [Další informace](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).