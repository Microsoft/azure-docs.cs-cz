---
title: Běžné otázky týkající se zařízení Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se zařízení Azure Migrate.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 3bb066b08447a951665e629da5ebcb75714b9f1e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425351"
---
# <a name="azure-migrate-appliance-common-questions"></a>Zařízení Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se zařízení Azure Migrate. Pokud máte další otázky, přečtěte si tyto články:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- [Dotazy](common-questions-discovery-assessment.md) týkající se vizualizace zjišťování, hodnocení a závislostí.
- [Dotazy](common-questions-server-migration.md) týkající se migrace serveru
- Získejte odpovědi na otázky na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum). 


## <a name="what-is-the-azure-migrate-appliance"></a>Co je zařízení Azure Migrate?

Zařízení Azure Migrate je jednoduché zařízení, které používá nástroj Azure Migrate: Server Assessment Tool ke zjišťování a vyhodnocení místních serverů. Toto zařízení používá nástroj Azure Migrate: Server pro migraci pro migraci místních virtuálních počítačů VMware bez agenta. 

- Zařízení je nasazené místně jako virtuální počítač nebo fyzický počítač.
- Zařízení zjišťuje místní počítače a průběžně odesílá metadata a data o výkonu počítačů do Azure Migrate.
- Zjišťování zařízení je bez agenta. Na zjištěných počítačích není nic nainstalované.

[Přečtěte si další informace](migrate-appliance.md) o zařízení.

## <a name="how-does-the-appliance-connect-to-azure"></a>Jak se zařízení připojuje k Azure?

Zařízení se může připojit přes Internet nebo používat Azure ExpressRoute s veřejným partnerským vztahem/Microsoftu.

## <a name="does-appliance-analysis-impact-performance"></a>Má dopad na analýzu vliv na výkon zařízení?

Zařízení Azure Migrate profilování místních počítačů neustále měří údaje o výkonu. Tato profilace nemá skoro žádné ovlivněné počítače s výkonem.

### <a name="can-i-harden-the-appliance-vm"></a>Můžu posílit svůj virtuální počítač zařízení?

Když vytvoříte virtuální počítač zařízení pomocí stažené šablony, můžete do šablony přidat komponenty (například antivirovou ochranu), pokud ponecháte komunikaci a pravidla brány firewall požadovaná pro Azure Migrate zařízení.


## <a name="what-network-connectivity-is-needed"></a>Jaké síťové připojení je potřeba?

Zkontrolujte informace o připojení k síti:
- Vyhodnocení VMware pomocí Azure Migrate zařízení: požadavky [URL](migrate-appliance.md#url-access) a přístup k [portu](migrate-support-matrix-vmware.md#port-access) .
- Migrace bez agentů VMware pomocí zařízení Azure Migrate: [adresy URL](migrate-appliance.md#url-access) a požadavky na přístup k [portu](migrate-support-matrix-vmware-migration.md#agentless-ports) .
- Posouzení technologie Hyper-V pomocí zařízení Azure Migrate: požadavků na [adresu URL](migrate-appliance.md#url-access) a přístup k [portu](migrate-support-matrix-hyper-v.md#port-access) .


## <a name="what-data-does-the-appliance-collect"></a>Jaká data shromažďuje zařízení?

Zkontrolujte shromážděná data:

- Data a [metadata](migrate-appliance.md#collected-metadata-vmware)o [výkonu](migrate-appliance.md#collected-performance-data-vmware) virtuálního počítače VMware.
- Data a [metadata](migrate-appliance.md#collected-metadata-hyper-v) [výkonu](migrate-appliance.md#collected-performance-data-hyper-v) virtuálního počítače Hyper-V.


## <a name="how-is-data-stored"></a>Jak se data ukládají?

Data shromažďovaná zařízením Azure Migrate se ukládají do umístění Azure, ve kterém jste vytvořili projekt Azure Migrate. 

- Data jsou bezpečně uložená v rámci předplatného Microsoft a odstraňují se při odstranění Azure Migrate projektu.
- Pokud používáte [vizualizaci závislostí](concepts-dependency-visualization.md), shromážděná data jsou uložená v USA v pracovním prostoru Log Analytics vytvořeném v rámci předplatného Azure. Tato data se odstraní při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného.

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>Kolik dat se nahrává při průběžném profilaci?

Objem dat odesílaných do Azure Migrate závisí na počtu parametrů. Chcete-li poskytnout smysl, Azure Migrate projekt s 10 počítači (každý s jedním diskem a jedním síťovým ROZHRANÍm) posílá přibližně 50 MB za den. Tato hodnota je přibližná a mění se podle počtu datových bodů pro síťové karty a disky. Zvýšení odeslaných dat je nelineární, pokud dojde ke zvýšení počtu počítačů, síťových karet nebo disků.

## <a name="is-data-encrypted-at-restin-transit"></a>Zašifrují se data při přenosu po klidovém provozu?

Ano, pro obojí.

- Metadata se bezpečně odesílají do služby Azure Migrate přes Internet prostřednictvím protokolu HTTPS.
- Metadata se ukládají do databáze [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) a do služby [Azure Blob Storage](../storage/common/storage-service-encryption.md)v předplatném Microsoftu. Metadata jsou šifrovaná v klidovém stavu.
- Data pro analýzu závislostí se také šifrují při přenosu (zabezpečené HTTPS). Je uložený v pracovním prostoru Log Analytics v rámci vašeho předplatného. Také se zašifruje v klidovém stavu.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Jak se zařízení připojuje k vCenter Server?

1. Zařízení se připojí k vCenter Server (port 443) s použitím přihlašovacích údajů, které jste zadali při nastavení zařízení.
2. Zařízení používá VMware PowerCLI k dotazování vCenter Server pro shromažďování metadat o virtuálních počítačích spravovaných pomocí vCenter Server.
3. Zařízení shromažďuje konfigurační data o virtuálních počítačích (jádrech, paměti, discích, síťových rozhraních) a historii výkonu každého virtuálního počítače za minulý měsíc.
4. Shromážděná metadata jsou odeslána do Azure Migrate: vyhodnocení serveru (přes Internet prostřednictvím protokolu HTTPS) pro posouzení.

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>Můžu zařízení připojit k více serverům vCenter?

Ne. Mezi zařízením a vCenter Server existuje mapování 1:1. Chcete-li zjistit virtuální počítače ve více instancích vCenter Server, je nutné nasadit více zařízení.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Kolik virtuálních počítačů nebo serverů můžu zjistit pomocí zařízení?

Můžete zjistit až 10 000 virtuálních počítačů VMware, až 5 000 virtuálních počítačů Hyper-V, a až 250 fyzické servery s jedním zařízením. Pokud máte ve svém místním prostředí více počítačů, přečtěte si informace o škálování [technologie Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) a [fyzické](scale-physical-assessment.md) posouzení.

## <a name="can-i-delete-an-appliance"></a>Můžu odstranit zařízení?

Odstranění zařízení z projektu v současné době se nepodporuje.

- Jediným způsobem, jak zařízení odstranit, je odstranit skupinu prostředků, která obsahuje Azure Migrate projekt přidružený k danému zařízení.
- Odstraněním skupiny prostředků se ale odstraní taky další registrovaná zařízení, zjištěná inventarizace, posouzení a všechny ostatní komponenty Azure přidružené k projektu ve skupině prostředků.


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>Můžu zařízení používat s jiným předplatným nebo projektem?

Po použití zařízení k inicializaci zjišťování ho nemůžete znovu nakonfigurovat s jiným předplatným Azure, nebo ho použít v jiném Azure Migrateovém projektu. Virtuální počítače nemůžete najít ani na jiném vCenter Server. Nastavte nové zařízení pro tyto úlohy.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Můžu zařízení nastavit na virtuálním počítači Azure?
Ne. Toto není aktuálně podporováno. 

## <a name="can-i-discover-on-an-esxi-host"></a>Můžu na hostiteli ESXi zjistit?
Ne. K vyhledání virtuálních počítačů VMware potřebujete vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Návody aktualizovat zařízení?

Ve výchozím nastavení se zařízení a jeho instalované agenti aktualizují automaticky. Zařízení zkontroluje aktualizace každých 24 hodin. Pokud dojde k chybě procesu aktualizace, dojde k opakovanému pokusu. Automatické aktualizace aktualizují jenom agenty zařízení a zařízení. Operační systém se neaktualizuje. Pomocí aktualizací Microsoftu Udržujte operační systém v aktuálním stavu.

## <a name="can-i-check-agent-health"></a>Můžu kontrolovat stav agenta?

Na portálu navštivte stránku **Stav agenta** v nástroji Server Assessment nebo server pro migraci. Tady můžete kontrolovat stav připojení mezi agenty zjišťování a hodnocení na zařízení a v Azure.

## <a name="next-steps"></a>Další kroky
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
