---
title: Přehled funkcí zabezpečení
description: Seznamte se s funkcemi zabezpečení v azure backupu, které vám pomohou chránit vaše záložní data a splnit bezpečnostní potřeby vaší firmy.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 91a0f29862b0c9c35e562c143e28ebbc6c39cf94
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423181"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Přehled funkcí zabezpečení v Azure Backup

Jedním z nejdůležitějších kroků, které můžete provést k ochraně dat, je mít spolehlivou infrastrukturu zálohování. Stejně důležité však je zajistit, aby vaše data byla zálohována bezpečným způsobem a aby vaše zálohy byly vždy chráněny. Azure Backup poskytuje zabezpečení pro vaše prostředí zálohování – jak při přenosu dat, tak v klidovém stavu. Tento článek uvádí funkce zabezpečení v Azure Backup, které vám pomohou chránit vaše záložní data a splňují bezpečnostní potřeby vaší firmy.

## <a name="management-and-control-of-identity-and-user-access"></a>Správa a kontrola identity a přístupu uživatelů

Azure Backup umožňuje spravovat jemně odstupňovaný přístup pomocí [Řízení přístupu na základě rolí Azure (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). RBAC umožňuje oddělit povinnosti v rámci vašeho týmu a udělit pouze množství přístupu k uživatelům potřebné k výkonu jejich práce.

Azure Backup poskytuje tři integrované role pro řízení operací správy zálohování:

* Přispěvatel zálohování – vytváření a správa záloh, s výjimkou odstranění trezoru služby Recovery Services a poskytnutí přístupu ostatním
* Operátor zálohování – vše, co přispěvatel dělá, kromě odebrání zásad zálohování a správy zálohování
* Backup Reader - oprávnění k zobrazení všech operací správy zálohování

Další informace o [řízení přístupu na základě rolí pro správu Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Služba Azure Backup má ve službě několik ovládacích prvků zabezpečení, které zabraňují chybám zabezpečení, zjišťují je a reagují na ně. Další informace o [ovládacích prvcích zabezpečení pro Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Oddělení hosta a úložiště Azure

Díky službě Azure Backup, která zahrnuje zálohování virtuálních strojů a sql a SAP HANA v zálohování virtuálních počítačů, se data zálohování ukládají v úložišti Azure a host nemá přímý přístup k úložišti záloh nebo k jeho obsahu.  Se zálohováním virtuálního počítače se vytváření a ukládání snímků záloh provádí pomocí prostředků infrastruktury Azure, kde host nemá žádnou účast než neutuchající úlohy pro zálohy konzistentní s aplikacemi.  S SQL a SAP HANA rozšíření zálohy získá dočasný přístup k zápisu na konkrétní objekty BLOB.  Tímto způsobem ani v ohroženém prostředí nelze s existujícími zálohami manipulovat ani je host odstranit.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Připojení k Internetu není vyžadováno pro zálohování virtuálních počítači Azure

Zálohování virtuálních počítačů Azure vyžaduje přesun dat z disku virtuálního počítače do trezoru služby Recovery Services. Všechny požadované komunikace a přenos dat se však děje jenom v páteřní síti Azure bez nutnosti přístupu k virtuální síti. Zálohování virtuálních počítačů Azure umístěných v zabezpečených sítích proto nevyžaduje povolení přístupu k žádným IP sítím nebo souhrnným souborům.

## <a name="private-endpoints-for-azure-backup"></a>Privátní koncové body pro zálohování Azure

Nyní můžete použít [soukromé koncové body](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) k bezpečnému zálohování dat ze serverů uvnitř virtuální sítě do trezoru služby Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor, takže nemusíte vystavovat virtuální sítě žádné veřejné IP adresy. Privátní koncové body se můžou použít k zálohování a obnovení databází SQL a SAP HANA, které běží uvnitř virtuálních počítačů Azure. Lze jej také použít pro místní servery pomocí agenta MARS.

>[!NOTE]
> Tato funkce je v současné době v omezené dostupnosti. Vyplňte [tento průzkum](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) a [pošlete nám e-mail,](mailto:azbackupnetsec@microsoft.com) pokud máte zájem o použití privátní koncové body pro Azure Backup. Možnost používat tuto funkci podléhá schválení službou Azure Backup.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Šifrování dat v tranzitu a v klidovém stavu

Šifrování chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů. V rámci Azure jsou data při přenosu mezi úložištěm Azure a trezorem chráněná protokolem HTTPS. Tato data zůstanou v páteřní síti Azure.

* Záložní data jsou automaticky šifrována pomocí klíčů spravovaných společností Microsoft. Zálohované virtuální počítače se spravovaným diskem můžete také šifrovat v trezoru služby Recovery Services pomocí [klíčů spravovaných zákazníky](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) uložených v trezoru klíčů Azure. K povolení tohoto šifrování není nutné provádět žádné explicitní akce. Vztahuje se na všechny úlohy zálohované do trezoru služby Recovery Services.

* Azure Backup podporuje zálohování a obnovení virtuálních počítačů Azure, které mají své operační sem/datové disky zašifrované pomocí Azure Disk Encryption (ADE). [Další informace o šifrovaných virtuálních počítačích Azure a azure backupu](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrana záložních dat před neúmyslným odstraněním

Azure Backup poskytuje funkce zabezpečení, které pomáhají chránit záložní data i po odstranění. Při odstranění pomocí funkce Soft Delete, pokud uživatel odstraní zálohu virtuálního počítače, budou záložní data zachována dalších 14 dní, což umožní obnovení této položky zálohy bez ztráty dat. Dalších 14 dnů uchovávání záložních dat ve stavu "obnovitelné odstranění" nevznikne žádné náklady pro zákazníka. [Další informace o obnovitelném odstranění](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Sledování a upozornění na podezřelou aktivitu

Azure Backup poskytuje [integrované funkce monitorování a upozorňování](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) k zobrazení a konfiguraci akcí souvisejících s Azure Backup. [Sestavy zálohování](https://docs.microsoft.com/azure/backup/configure-reports) slouží jako one-stop cíl pro sledování využití, auditování záloh a obnovení a identifikaci klíčových trendů na různých úrovních rozlišovací schopnosti. Pomocí nástrojů azure zálohování monitorování a vytváření sestav můžete upozornit na jakékoli neoprávněné, podezřelé nebo škodlivé aktivity, jakmile k nim dojde.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funkce zabezpečení pomáhající chránit hybridní zálohy

Služba Azure Backup používá agenta Služby microsoft azure recovery services (MARS) k zálohování a obnovení souborů, složek a stavu svazku nebo systému z místního počítače do Azure. MARS nyní poskytuje funkce zabezpečení, které pomáhají chránit hybridní zálohy. Mezi tyto funkce patří:

* Další vrstva ověřování je přidána vždy, když je provedena kritická operace, jako je změna přístupového hesla. Toto ověření je zajistit, že tyto operace lze provádět pouze uživatelé, kteří mají platné přihlašovací údaje Azure. [Přečtěte si další informace o funkcích, které brání útokům](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* Odstraněná data zálohování jsou uchovávána dalších 14 dní od data odstranění. Tím je zajištěna obnovitelnost dat v daném časovém období, takže nedojde ke ztrátě dat, i když dojde k útoku. Také větší počet minimální body obnovení jsou udržovány na ochranu proti poškozeným datům. [Další informace o obnovení odstraněných záložních dat](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

## <a name="compliance-with-standardized-security-requirements"></a>Dodržování standardizovaných bezpečnostních požadavků

Microsoft Azure & Azure Backup nabízí komplexní sadu certifikací a atestationů, které organizacím pomáhají dodržovat národní, regionální a oborové požadavky, kterými se řídí shromažďování a používání dat jednotlivců. [Podívejte se na seznam certifikací dodržování předpisů](compliance-offerings.md)

## <a name="next-steps"></a>Další kroky

* [Funkce zabezpečení pomáhají chránit cloudové úlohy, které používají Azure Backup](backup-azure-security-feature-cloud.md)
* [Funkce zabezpečení pomáhají chránit hybridní zálohy, které používají Azure Backup](backup-azure-security-feature.md)
