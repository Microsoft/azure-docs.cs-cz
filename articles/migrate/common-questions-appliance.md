---
title: Nejčastější dotazy k zařízení Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se zařízení Azure Migrate.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2ef7de3b31cb11a71ec9379232fc5ff1022cf666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336973"
---
# <a name="azure-migrate-appliance-common-questions"></a>Zařízení Pro migraci Azure: Běžné otázky

Tento článek odpovídá na běžné otázky týkající se zařízení Azure Migrate. Máte-li další dotazy, zkontrolujte tyto zdroje:

- [Obecné otázky týkající](resources-faq.md) se migrace Azure
- Otázky týkající se [vizualizace zjišťování, hodnocení a závislosti](common-questions-discovery-assessment.md)
- Otázky týkající se [migrace serveru](common-questions-server-migration.md)
- Získejte odpovědi na otázky ve [fóru Migrace Azure](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Co je zařízení Azure Migrate?

Zařízení Azure Migrate je zjednodušené zařízení, které nástroj Azure Migrate: Server Assessment používá ke zjišťování a hodnocení místních serverů. Nástroj Migrace Azure: Migrace serveru také používá zařízení pro migraci místních virtuálních počítačů VMware bez agentů.

Tady jsou další informace o zařízení Azure Migrate:

- Zařízení se nasadí místně jako virtuální počítač nebo fyzický počítač.
- Zařízení zjišťuje místní počítače a průběžně odesílá metadata a údaje o výkonu počítače do Migrace Azure.
- Zjišťování zařízení je bez agenta. V zjištěných počítačích není nic nainstalováno.

[Další informace](migrate-appliance.md) o přístroji.

## <a name="how-does-the-appliance-connect-to-azure"></a>Jak se zařízení připojuje k Azure?

Zařízení se může připojit přes internet nebo pomocí Azure ExpressRoute s veřejným partnerem microsoftu.

## <a name="does-appliance-analysis-affect-performance"></a>Má analýza zařízení vliv na výkon?

Azure Migrate zařízení profily místní chod počítače nepřetržitě k měření dat o výkonu. Toto profilování nemá téměř žádný vliv na výkon profilovaných počítačů.

## <a name="can-i-harden-the-appliance-vm"></a>Mohu zpevnit virtuální zařízení VM?

Při použití stažené šablony k vytvoření virtuálního počítače zařízení, můžete přidat komponenty (antivirus, například) do šablony, pokud ponecháte na místě pravidla komunikace a brány firewall, které jsou požadovány pro zařízení Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Jaké připojení k síti je vyžadováno?

Informace o požadavcích na připojení k síti pro zařízení Azure Migrate naleznete v následujících článcích:

- **Hodnocení vmware:** [Přístup k adresám URL](migrate-appliance.md#url-access) a přístup k [portům](migrate-support-matrix-vmware.md#port-access)
- **Migrace bez agentů VMware**: [Přístup k adresám URL](migrate-appliance.md#url-access) a přístup k [portům](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Hodnocení Hyper-V:** [Přístup k url](migrate-appliance.md#url-access) a přístup k [portům](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>Jaká data přístroj shromažďuje?

Informace o datech, která zařízení Azure Migrate shromažďuje na virtuálních počítačích, najdete v následujících článcích:

- **VMware VM**: [Zkontrolujte](migrate-appliance.md#collected-data---vmware) shromážděná data. [
- **Virtuální vod Hyper-V**: [Zkontrolujte](migrate-appliance.md#collected-data---hyper-v) shromážděná data.

## <a name="how-is-data-stored"></a>Jak se data ukládají?

Data shromážděná zařízením Azure Migrate se ukládají v umístění Azure, kde jste vytvořili projekt Migrace Azure.

Další informace o způsobu ukládání dat:

- Shromážděná data jsou bezpečně uložena v CosmosDB v předplatném společnosti Microsoft. Data se odstraní při odstranění projektu Migrace Azure. Úložiště je zpracováno Azure Migrate. Pro shromážděná data nelze konkrétně vybrat účet úložiště.
- Pokud používáte [vizualizaci závislostí](concepts-dependency-visualization.md), shromažďovaná data se ukládají ve Spojených státech v pracovním prostoru Azure Log Analytics vytvořeném ve vašem předplatném Azure. Data se odstraní, když odstraníte pracovní prostor Log Analytics ve vašem předplatném.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Kolik dat se nahrává během průběžného profilování?

Objem dat, která se odesílá do Migrace Azure závisí na více parametrech. Jako příklad projekt Migrace Azure, který má 10 počítačů (každý s jedním diskem a jednou nokovou pomocí) odešle přibližně 50 MB dat za den. Tato hodnota je přibližná; skutečná hodnota se liší v závislosti na počtu datových bodů pro disky a síťové karty. Pokud se zvýší počet počítačů, disků nebo síťových karty, zvýšení dat, která je odeslána je nelineární.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Jsou data šifrována v klidovém stavu a při přenosu?

Ano, pro oba:

- Metadata se bezpečně odesílají do služby Azure Migrate přes internet přes protokol HTTPS.
- Metadata se ukládají v databázi [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) a v [úložišti objektů Blob Azure](../storage/common/storage-service-encryption.md) v předplatném Microsoftu. Metadata jsou šifrována v klidovém stavu pro úložiště.
- Data pro analýzu závislostí jsou také šifrována při přenosu (zabezpečeným protokolem HTTPS). Je uloženv pracovním prostoru Log Analytics ve vašem předplatném. Data jsou šifrována v klidovém stavu pro analýzu závislostí.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Jak se zařízení připojuje k serveru vCenter?

Tyto kroky popisují, jak se zařízení připojuje k serveru VMware vCenter:

1. Zařízení se připojí k serveru vCenter (port 443) pomocí přihlašovacích údajů, které jste zadali při nastavovat zařízení.
2. Zařízení používá VMware PowerCLI k dotazování serveru vCenter ke shromažďování metadat o virtuálních serverech, které jsou spravovány serverem vCenter.
3. Zařízení shromažďuje konfigurační data o virtuálních počítačích (jádra, paměť, disky, síťové karty) a historii výkonu každého virtuálního počítače za poslední měsíc.
4. Shromážděná metadata se posílají do nástroje Migrace: Vyhodnocení serveru (přes internet přes protokol HTTPS) k vyhodnocení.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Může se zařízení Azure Migrate připojit k více serverům vCenter?

Ne. Mezi [zařízením Azure Migrate](migrate-appliance.md) a serverem vCenter je mapování 1:1. Chcete-li zjistit virtuální chod na více instancích serveru vCenter, je nutné nasadit více zařízení. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Může mít projekt Migrace Azure více zařízení?
Projekt může mít více zařízení k němu připojen. Zařízení však může být přidruženo pouze k jednomu projektu. 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Kolik virtuálních zařízení nebo serverů můžu zjistit pomocí zařízení?

Můžete objevit až 10 000 virtuálních zařízení VMware, až 5 000 virtuálních virtuálních zařízení Hyper-V a až 250 fyzických serverů s jedním zařízením. Pokud máte více počítačů v místním prostředí, přečtěte si o [škálování hodnocení Hyper-V](scale-hyper-v-assessment.md), [škálování hodnocení VMware](scale-vmware-assessment.md)a [škálování hodnocení fyzického serveru](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Mohu spotřebič smazat?

V současné době není podporováno odstranění zařízení z projektu.

Jediný způsob, jak odstranit zařízení je odstranit skupinu prostředků, která obsahuje projekt Azure Migrate, který je přidružen k zařízení.

Odstranění skupiny prostředků však také odstraní další registrovaná zařízení, zjištěný inventář, hodnocení a všechny ostatní součásti Azure ve skupině prostředků, které jsou přidruženy k projektu.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Mohu přístroj používat s jiným předplatným nebo projektem?

Po použití zařízení k zahájení zjišťování, nelze překonfigurovat zařízení pro použití s jiným předplatným Azure a nelze použít v jiném projektu Azure Migrate. Také nelze zjistit virtuální chod na jinou instanci vCenter Server. Nastavte pro tyto úkoly nový spotřebič.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Můžu nastavit zařízení na virtuálním počítači Azure?

Ne. V současné době tato možnost není podporována. 

## <a name="can-i-discover-on-an-esxi-host"></a>Mohu to zjistit na hostiteli ESXi?

Ne. Chcete-li zjistit virtuální manové servery VMware, musíte mít server vCenter.

## <a name="how-do-i-update-the-appliance"></a>Jak mohu přístroj aktualizovat?

Ve výchozím nastavení se zařízení a jeho instaluje agenti automaticky aktualizují. Přístroj kontroluje aktualizace každých 24 hodin. Aktualizace, které se nezdaří, jsou opakovány. 

Tyto automatické aktualizace aktualizují pouze zařízení a zařízení. Operační systém není aktualizován Azure Migrovat automatické aktualizace. Chcete-li udržovat operační systém aktuální, použijte službu Aktualizace systému Windows.

## <a name="can-i-check-agent-health"></a>Mohu zkontrolovat stav agenta?

Ano. Na portálu přejděte na stránku **stavu agenta** pro nástroj Migrace: Vyhodnocení serveru nebo Azure Migrate: Migrace serveru. Zde můžete zkontrolovat stav připojení mezi Azure a zjišťování a hodnocení agentů na zařízení.

## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Migrace Azure](migrate-services-overview.md).
