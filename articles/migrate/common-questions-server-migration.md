---
title: Nejčastější dotazy k migraci serveru Azure
description: Získejte odpovědi na časté otázky týkající se migrace počítačů pomocí migrace serveru Azure.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: cc78d2087dcaad2922ca6b6d9c090a8decdb6e84
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393800"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migrace migrace serveru Azure: Běžné otázky

Tento článek odpovídá na běžné otázky týkající se nástroje Migrace Azure: Migrace serveru. Máte-li další dotazy, zkontrolujte tyto zdroje:

- [Obecné otázky týkající](resources-faq.md) se migrace Azure
- Otázky týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Otázky týkající se [vizualizace zjišťování, hodnocení a závislosti](common-questions-discovery-assessment.md)
- Získejte odpovědi na otázky ve [fóru Migrace Azure](https://aka.ms/AzureMigrateForum)

## <a name="how-does-agentless-vmware-replication-work"></a>Jak replikace VMware bez agenta funguje?

Metoda replikace bez agenta pro společnost VMware používá snímky VMware a sledování změn bloků (CBT) v systému VMware.

Postup je následující:

1. Při zahájení replikace je naplánován počáteční cyklus replikace. V počátečním cyklu se pořizovat snímek virtuálního počítače. Snímek se používá k replikaci virtuálních počítačů VMDK (disky). 
2. Po dokončení počátečního cyklu replikace jsou pravidelně naplánovány cykly rozdílové replikace.
    - Během rozdílové replikace je pořízen snímek a datové bloky, které se změnily od předchozího cyklu replikace jsou replikovány.
    - VMware CBT se používá k určení bloků, které se změnily od posledního cyklu.
    - Četnost cyklů pravidelné replikace se automaticky spravuje pomocí Migrace Azure a závisí na tom, kolik dalších virtuálních počítačů a disků se současně replikuje ze stejného úložiště dat. V ideálních podmínkách replikace nakonec konverguje na jeden cyklus za hodinu pro každý virtuální virtuální ms.

Při migraci je naplánován cyklus replikace na vyžádání, aby počítač zachytil všechna zbývající data. Chcete-li zajistit nulovou ztrátu dat a konzistenci aplikace, můžete během migrace vypnout počítač.

## <a name="why-isnt-resynchronization-exposed"></a>Proč není resynchronizace vystavena?

Během migrace bez agenta v každém cyklu delta je zapsán rozdíl mezi aktuální snímek a dříve pořízené snímek. Je to vždy rozdíl mezi snímky, skládání dat palců Pokud je určitý sektor zapsán *N* krát mezi snímky, pouze poslední zápis musí být převedeny, protože máme zájem pouze o poslední synchronizaci. Proces se liší od replikace založené na agentovi, během které sledujeme a aplikujeme každý zápis. V tomto procesu každý cyklus delta je resynchronizace. Takže žádná možnost resynchronizace vystavena. Pokud disky někdy nejsou synchronizovány z důvodu selhání, je stanovena v dalším cyklu. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Jak rychlost konve ovlivňuje replikaci bez agenta?

Vzhledem k tomu, že replikace bez agenta se skládá v datech, je *vzorek změn* důležitější než *rychlost změn*. Při zápisu souboru znovu a znovu, rychlost nemá velký vliv. Však vzor, ve kterém je zapsán každý jiný sektor způsobí vysoké změny v dalším cyklu. Vzhledem k tomu, že minimalizujeme množství dat, která přenášíme, umožňujeme, aby se data před naplánováním dalšího cyklu sloužují co nejvíce.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Jak často je naplánovaný cyklus replikace?

Vzorec pro naplánování dalšího replikačního cyklu je (předchozí doba cyklu / 2) nebo jedna hodina, podle toho, která hodnota je vyšší.

Například pokud virtuální hod trvá čtyři hodiny pro cyklus delta, další cyklus je naplánováno za dvě hodiny a ne v další hodině. Proces se liší ihned po počáteční replikaci, když je okamžitě naplánován první cyklus delta.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Jak replikace bez agenta ovlivňuje servery VMware?

Výsledkem replikace bez agentů je určitý dopad na výkon hostitelů vMware vCenter Server a VMware ESXi. Vzhledem k tomu, že replikace bez agenta používá snímky, spotřebovává videa VOPS v úložišti, takže je vyžadována určitá šířka pásma úložiště IOPS. Nedoporučujeme používat replikaci bez agenta, pokud máte omezení pro úložiště nebo iOP ve vašem prostředí.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Můžu provést bezagentní migraci virtuálních montovek UEFI do Azure Gen 2?

Ne. Pomocí Azure Site Recovery migrujte tyto virtuální počítače do virtuálních počítačů Gen 2 Azure. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Můžu při migraci připnout virtuální počítače k zónám dostupnosti Azure?

Ne. Zóny dostupnosti Azure nejsou podporované pro migraci Azure.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Jaký přenosový protokol používá Azure Migrate během replikace?

Azure Migrate používá protokol NBD (Network Block Device) s šifrováním SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Jaká je minimální verze serveru vCenter potřebná pro migraci?

Musíte mít alespoň vCenter Server 5.5 a vSphere ESXi hostitele verze 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Můžou zákazníci migrovat své virtuální počítače na nespravované disky?

Ne. Azure Migrate podporuje migraci jenom na spravované disky (Standard HDD, Premium SSD).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Kolik virtuálních virtuálních mů se dá replikovat najednou pomocí migrace bez agentů?

V současné době můžete migrovat 100 virtuálních ms na instanci serveru vCenter server současně. Migrovat v dávkách 10 virtuálních discích.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Jak můžu omezit replikaci při používání zařízení Azure Migrate pro replikaci vmware bez agenta?  

Můžete omezení pomocí NetQosPolicy. Příklad:

AppNamePrefix pro použití v NetQosPolicy je "GatewayWindowsService.exe". Můžete vytvořit zásadu na zařízení Azure Migrate k omezení replikace provoz z zařízení vytvořením zásady, jako je tento:
 
New-NetQosPolicy -Název "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Kdy mám migrovat počítače jako fyzické servery?

Migrace počítačů tak, že je zacházíte jako s fyzickými servery, je užitečná v řadě scénářů:

- Při migraci místních fyzických serverů.
- Pokud migrujete virtuální počítače virtualizované platformami, jako je Xen, KVM.
- Chcete-li migrovat virtuální chod y Hyper-V nebo VMware, pokud z nějakého důvodu nemůžete použít standardní proces migrace pro migraci [Hyper-V](tutorial-migrate-hyper-v.md)nebo [VMware.](server-migrate-overview.md) Například pokud nepoužíváte vMware vCenter a používáte pouze hostitele ESXi.
- Migrace virtuálních počítačů, které aktuálně běží v privátních cloudech do Azure
- Pokud chcete migrovat virtuální počítače spuštěné ve veřejných cloudech, jako jsou Amazon Web Services (AWS) nebo Google Cloud Platform (GCP), do Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>Nasadil jsem dvě (nebo více) zařízení ke zjišťování virtuálních připojení na mém serveru vCenter. Však při pokusu o migraci virtuálních zařízení, vidím pouze virtuální chod odpovídající jeden z zařízení.

I když to může být dobrý případ použití, v současné době nepodporujeme. Nasazení dvou (nebo více) zařízení ke zjištění stejné sady virtuálních her způsobí problém se službou, ve kterém vlastnictví virtuálních her udržuje přepínání mezi dvěma zařízeními. To je důvod, proč se virtuální maže zobrazují a mizí. V takových případech je nutné problém vyřešit a provést pevný interval.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Potřebuji k migraci virtuálních měn VMware vCenter?
Chcete-li [migrovat virtuální počítačvsystému VMware](server-migrate-overview.md) pomocí migrace založené na agentech společnosti VMware nebo bez agentů, musí být hostitelé ESXi, na kterých jsou virtuální servery umístěny, spravováni serverem vCenter Server. Pokud nemáte vCenter Server, můžete migrovat virtuální chod VMware migrací jako fyzické servery. [Další informace](migrate-support-matrix-physical-migration.md).
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Migrace Azure](migrate-services-overview.md).
