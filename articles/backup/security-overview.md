---
title: Přehled funkcí zabezpečení
description: Seznamte se s možnostmi zabezpečení v Azure Backup, které vám pomůžou chránit data záloh a splňovat požadavky vaší firmy na zabezpečení.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 750ad7b10969ef5f83e0b5058e350066d3f97351
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062595"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Přehled funkcí zabezpečení v Azure Backup

Jedním z nejdůležitějších kroků, které můžete provést při ochraně dat, je mít spolehlivou infrastrukturu zálohování. Je to ale stejně důležité k tomu, aby se zajistilo, že se data zálohují zabezpečeným způsobem a že vaše zálohy jsou neustále chráněné. Azure Backup poskytuje zabezpečení pro vaše zálohovací prostředí – když jsou vaše data v provozu a v klidovém režimu. Tento článek obsahuje seznam funkcí zabezpečení v Azure Backup, které vám pomůžou chránit data záloh a splňovat požadavky vaší firmy na zabezpečení.

## <a name="management-and-control-of-identity-and-user-access"></a>Správa a řízení identit a přístupu uživatelů

Účty úložiště používané trezory služby Recovery Services jsou izolované a uživatelé k nim nemají k dispozici žádné škodlivé účely. Přístup je povolený jenom prostřednictvím operací správy Azure Backup, jako je například obnovení. Azure Backup vám umožní řídit spravované operace prostřednictvím jemně odstupňovaného přístupu pomocí [řízení přístupu na základě role Azure (RBAC)](./backup-rbac-rs-vault.md). RBAC umožňuje oddělení povinností v rámci týmu a udělení přístupu pouze k uživatelům potřebným ke své práci.

Azure Backup poskytuje tři [předdefinované role](../role-based-access-control/built-in-roles.md) pro řízení operací správy zálohování:

* Přispěvatel záloh – pro vytváření a správu záloh s výjimkou odstranění Recovery Services trezoru a poskytnutí přístupu jiným uživatelům
* Operátor zálohování – všechno, co Přispěvatel s výjimkou odebrání zálohování a správy zásad zálohování
* Čtečka zálohování – oprávnění k zobrazení všech operací správy zálohování

Přečtěte si další informace o [řízení přístupu na základě rolí ke správě Azure Backup](./backup-rbac-rs-vault.md).

Azure Backup obsahuje několik ovládacích prvků zabezpečení, které jsou součástí služby pro prevenci, detekci a reakci na slabá místa zabezpečení. Přečtěte si další informace o [ovládacích prvcích zabezpečení pro Azure Backup](./backup-security-controls.md).

## <a name="separation-between-guest-and-azure-storage"></a>Oddělení mezi hostem a službou Azure Storage

Pomocí Azure Backup, která zahrnuje zálohování virtuálních počítačů a SQL a SAP HANA v zálohování virtuálních počítačů, se zálohovaná data ukládají do služby Azure Storage a Host nemá přímý přístup k úložišti záloh nebo jeho obsahu.  Při zálohování virtuálních počítačů se vytváření a ukládání snímků záloh provádějí pomocí prostředků infrastruktury Azure, u kterých nemá Host žádnou jinou účast než uvedení úlohy do nečinnosti pro zálohy konzistentní s aplikací.  Pomocí SQL a SAP HANA načte záložní rozšíření dočasný přístup pro zápis do konkrétních objektů BLOB.  Tímto způsobem i v případě ohroženého prostředí nelze stávající zálohy úmyslně nebo odstranit pomocí hosta.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Pro zálohování virtuálních počítačů Azure se nevyžaduje připojení k Internetu.

Zálohování virtuálních počítačů Azure vyžaduje přesun dat z disku virtuálního počítače do trezoru Recovery Services. Veškerá požadovaná komunikace a přenos dat se ale stane jenom v páteřní síti Azure, aniž by bylo potřeba mít přístup k virtuální síti. Zálohování virtuálních počítačů Azure umístěných v zabezpečených sítích proto nevyžaduje povolení přístupu k žádným IP adresám nebo plně kvalifikovaným názvům domén.

## <a name="private-endpoints-for-azure-backup"></a>Soukromé koncové body pro službu Azure Backup

Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md) teď můžete data bezpečně zálohovat ze serverů ve virtuální síti do svého trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor, takže nemusíte zveřejňovat virtuální sítě pro žádné veřejné IP adresy. Pomocí privátních koncových bodů se dají zálohovat a obnovovat databáze SQL a SAP HANA, které běží v rámci virtuálních počítačů Azure. Dá se taky použít pro vaše místní servery pomocí agenta MARS.

[Tady](./private-endpoints.md)si můžete přečíst další informace o privátních koncových bodech pro Azure Backup.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Šifrování dat při přenosu a v klidovém režimu

Šifrování chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. V rámci Azure jsou data přenášená mezi službou Azure Storage a trezorem chráněná protokolem HTTPS. Tato data zůstávají v páteřní síti Azure.

* Zálohovaná data se automaticky šifrují pomocí klíčů spravovaných Microsoftem. Pomocí [zákaznických klíčů](backup-encryption.md#encryption-of-backup-data-using-customer-managed-keys) uložených v Azure Key Vault můžete také zašifrovat zálohované virtuální počítače se spravovanými disky v úložišti Recovery Services. K povolení tohoto šifrování nemusíte provádět žádnou explicitní akci. Platí pro všechny úlohy, které se zálohují do vašeho trezoru Recovery Services.

* Azure Backup podporuje zálohování a obnovení virtuálních počítačů Azure, které mají svoje disky s operačním systémem nebo daty šifrované pomocí Azure Disk Encryption (ADE). [Přečtěte si další informace o šifrovaných virtuálních počítačích Azure a Azure Backup](./backup-azure-vms-encryption.md).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrana zálohovaných dat před neúmyslnými odstraněními

Azure Backup poskytuje funkce zabezpečení, které vám pomůžou chránit zálohovaná data i po jejím odstranění. Pokud uživatel odstraní zálohu virtuálního počítače pomocí obnovitelného odstranění, uchovávají se data zálohy po dobu 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohovaných dat ve stavu "obnovitelné odstranění" neúčtují žádné náklady na zákazníka. [Přečtěte si další informace o obnovitelném odstranění](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorování a výstrahy podezřelé aktivity

Azure Backup poskytuje [integrované možnosti monitorování a upozorňování](./backup-azure-monitoring-built-in-monitor.md) pro zobrazení a konfiguraci akcí pro události související s Azure Backup. [Sestavy zálohování](./configure-reports.md) slouží jako zastavení pro sledování využití, auditování záloh a obnovení a určení klíčových trendů v různých úrovních členitosti. Pomocí nástrojů pro monitorování a vytváření sestav Azure Backup vás upozorní na jakékoli neoprávněné, podezřelé nebo škodlivé aktivity, jakmile k nim dojde.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy

Služba Azure Backup používá agenta Microsoft Azure Recovery Services (MARS) k zálohování a obnovení souborů, složek a svazku nebo stavu systému z místního počítače do Azure. MARS nyní poskytuje funkce zabezpečení, které vám pomůžou chránit hybridní zálohy. Patří k nim:

* Další úroveň ověřování se přidá vždy, když se provede kritická operace, jako je změna hesla. Ověření je zajištěno, že tyto operace mohou provádět pouze uživatelé, kteří mají platné přihlašovací údaje Azure. [Přečtěte si další informace o funkcích, které zabraňují útokům](./backup-azure-security-feature.md#prevent-attacks).

* Data odstraněných záloh se uchovávají po dobu dalších 14 dní od data odstranění. Tím je zajištěna obnova dat během daného časového období, takže nedochází ke ztrátě dat, i když dojde k útoku. Pro ochranu před poškozenými daty je také udržován větší počet minimálních bodů obnovení. [Přečtěte si další informace o obnově odstraněných zálohovaných dat](./backup-azure-security-feature.md#recover-deleted-backup-data).

* Pro data zálohovaná pomocí agenta Microsoft Azure Recovery Services (MARS) se před odesláním do Azure Backup a dešifrují data, která se zašifrují po stažení z Azure Backup, k zajištění šifrování dat. Podrobnosti přístupového hesla jsou dostupné jenom uživateli, který vytvořil heslo, a agenta, který je s ním nakonfigurovaný. Žádná služba není přenášená ani sdílená se službou. Tím zajistíte, že se data, která se nechtěně zveřejňují (například útok prostředníkem na síť), dokončí bez hesla a heslo se v síti nepošle.

## <a name="compliance-with-standardized-security-requirements"></a>Dodržování standardních požadavků na zabezpečení

Aby organizace dodržovaly národní, regionální a specifické požadavky na konkrétní odvětví pro shromažďování a používání dat jednotlivců, Microsoft Azure & Azure Backup nabízí komplexní sadu certifikací a osvědčení. [Zobrazit seznam certifikace dodržování předpisů](compliance-offerings.md)

## <a name="next-steps"></a>Další kroky

* [Funkce zabezpečení, které vám pomůžou chránit cloudové úlohy, které používají Azure Backup](backup-azure-security-feature-cloud.md)
* [Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy, které používají Azure Backup](backup-azure-security-feature.md)
