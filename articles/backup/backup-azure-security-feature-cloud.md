---
title: Funkce zabezpečení, které vám pomůžou chránit cloudové úlohy, které používají Azure Backup
description: Naučte se používat funkce zabezpečení v Azure Backup k zajištění většího zabezpečení záloh.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: f0e4540f3f5ab3fdbb5953cbf100c5fdc2b2542a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622013"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funkce zabezpečení, které vám pomůžou chránit cloudové úlohy, které používají Azure Backup

Obavy z problémů se zabezpečením, jako jsou malware, ransomware a vniknutí, neustále rostou. Tyto potíže mohou být nákladné, a to z hlediska financí i dat. Pro ochranu proti takovým útokům Azure Backup nyní poskytuje funkce zabezpečení, které vám pomůžou chránit zálohovaná data i po odstranění. Jednou z těchto funkcí je obnovitelné odstranění. Pomocí obnovitelného odstranění, a to i v případě, že škodlivý objekt actor odstraní zálohu virtuálního počítače (nebo se data záloh nechtěně odstraní), zálohovaná data se uchovávají po dobu 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Tyto další 14 dní uchovávání zálohovaných dat ve stavu "obnovitelné odstranění" neúčtují žádné náklady na zákazníka.

> [!NOTE]
> Obnovitelné odstranění chrání pouze Odstraněná data zálohy. Pokud se virtuální počítač odstraní bez zálohy, funkce obnovitelného odstranění nebude data uchovávat. Všechny prostředky by měly být chráněné pomocí Azure Backup, aby se zajistila plná odolnost.
>

## <a name="soft-delete"></a>Obnovitelné odstranění

### <a name="supported-regions"></a>Podporované oblasti

Obnovitelné odstranění se v současné době podporuje v Středozápadní USA, Východní Asie, Kanadě – střed, Kanada – východ, Francie – střed, Francie – jih, Jižní Korea, Korea – jih, Velká Británie – jih, Velká Británie – západ, Austrálie – východ, Austrálie – jihovýchod, Severní Evropa, Západní USA, západní USA 2, Střed USA, jih Východní Asie, Střed USA – sever, Střed USA – jih, Japonsko – východ, Japonsko – západ, Indie – jih, Indie – střed, Indie – západ, Východní USA 2, Švýcarsko – sever, Švýcarsko – západ a všechny národní oblasti.

### <a name="soft-delete-for-vms"></a>Obnovitelné odstranění pro virtuální počítače

1. Aby bylo možné odstranit zálohovaná data virtuálního počítače, je třeba zastavit zálohování. V Azure Portal přejděte do trezoru služby Recovery Services, klikněte pravým tlačítkem na zálohovanou položku a vyberte **Zastavit zálohování**.

   ![Snímek obrazovky Azure Portal zálohované položky](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. V následujícím okně budete mít možnost odstranit nebo zachovat data záloh. Pokud zvolíte možnost **Odstranit zálohovaná data** a pak **Zastavit zálohování**, zálohování virtuálního počítače se trvale neodstraní. Místo toho se zálohovaná data uchovávají 14 dnů ve stavu tichého odstranění. Pokud je zvolená možnost **Odstranit zálohovaná data** , pošle se na nakonfigurované ID e-mailu e-mailové upozornění s informací o uživateli, že pro zálohovaná data zbývá 14 dní rozšířené uchovávání dat. E-mailová výstraha se také pošle na 12. den, který vychází ze zbývajících dvou dnů, než se odobnovení Odstraněná data. Odstranění se odsadí do patnáctého dne, kdy dojde k trvalému odstranění a pošle se poslední e-mailová výstraha informující o trvalém odstranění dat.

   ![Snímek obrazovky Azure Portal, zastavení záložní obrazovky](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Během těchto 14 dnů se v Recovery Services trezoru u obnovitelného odstraněného virtuálního počítače zobrazí červená ikona "slabého odstranění" vedle něho.

   ![Snímek obrazovky Azure Portal, virtuální počítač ve stavu tichého odstranění](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Pokud se v trezoru nacházejí nějaké záložní zálohované položky, trezor se v této době nedá odstranit. Zkuste prosím odstranit trezor, až se zálohované položky trvale odstraní, a v trezoru se neodstraní žádná položka v tichém odstraněném stavu.

4. Aby bylo možné obnovit částečný odstraněný virtuální počítač, musí být nejprve obnoven. Pokud ho chcete obnovit, vyberte virtuální počítač s příjemným odstraněnou a pak klikněte na možnost **zrušit odstranění**.

   ![Snímek obrazovky Azure Portal, odstranění virtuálního počítače](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Zobrazí se okno upozornění, že pokud je zvoleno možnost zrušit odstranění, všechny body obnovení pro virtuální počítač budou neodstraněny a budou k dispozici pro operaci obnovení. Virtuální počítač se zachová ve stavu zastavit ochranu se zachováním dat a zálohy se pozastaví a zálohovaná data se trvale zachovají beze zásad zálohování.

   ![Snímek obrazovky Azure Portal, potvrďte odstranění virtuálního počítače.](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   V tuto chvíli můžete virtuální počítač obnovit taky tak, že z vybraného bodu obnovení vyberete **obnovit virtuální počítač** .  

   ![Snímek obrazovky Azure Portal, obnovení možnosti virtuálního počítače](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Systém uvolňování paměti spustí a vyčistí body obnovení s vypršenou platností až poté, co uživatel provede operaci **obnovení zálohy** .

5. Po dokončení procesu zrušení odstranění se stav vrátí do části zastavit zálohování s daty zachovat a pak můžete zvolit **pokračovat v zálohování**. Operace **obnovit zálohování** vrátí zpět zálohovanou položku v aktivním stavu, která je přidružená k zásadám zálohování vybraným uživatelem, který definuje plány zálohování a uchovávání.

   ![Snímek obrazovky Azure Portal, obnovit možnost zálohování](./media/backup-azure-security-feature-cloud/resume-backup.png)

Tento vývojový diagram znázorňuje různé kroky a stavy zálohované položky:

![Životní cyklus záložní položky odstraněné položky](./media/backup-azure-security-feature-cloud/lifecycle.png)

Další informace najdete v části [Nejčastější dotazy](backup-azure-security-feature-cloud.md#frequently-asked-questions) níže.

## <a name="disabling-soft-delete"></a>Zákaz obnovitelného odstranění

Obnovitelné odstranění je ve výchozím nastavení povolené u nově vytvořených trezorů. Pokud je funkce slabého odstranění zabezpečení zakázaná, zálohovaná data nebudou chráněná před náhodnými nebo škodlivými odstraněními. Bez funkce obnovitelného odstranění budou všechna odstranění chráněných položek výsledkem okamžitého odebrání bez možnosti obnovení. Vzhledem k tomu, že data zálohování ve stavu "obnovitelné odstranění" neúčtují žádné náklady na zákazníka, zakázání této funkce se nedoporučuje. Jediným případem, kdy byste měli zvážit zakázání obnovitelného odstranění, je, že plánujete přesunutí chráněných položek do nového trezoru a nemůžete počkat 14 dní před odstraněním a obnovením ochrany (například v testovacím prostředí).

### <a name="prerequisites-for-disabling-soft-delete"></a>Předpoklady pro zakázání obnovitelného odstranění

- Povolení nebo zakázání obnovitelného odstranění pro trezory (bez chráněných položek) může být provedeno pouze Azure Portal. To platí pro:
  - Nově vytvořené trezory, které neobsahují chráněné položky
  - Stávající trezory, jejichž chráněné položky byly odstraněny a jejichž platnost vypršela (mimo stanovenou dobu uchovávání za 14 dní)
- Pokud je funkce obnovitelného odstranění pro trezor zakázaná, můžete ji znovu povolit, ale tuto volbu nemůžete změnit a znovu ji zakázat, pokud trezor obsahuje chráněné položky.
- Nemůžete zakázat obnovitelné odstranění pro trezory, které obsahují chráněné položky nebo položky ve stavu nepodmíněného odstranění. Pokud to budete potřebovat, postupujte podle těchto kroků:
  - Zastavte ochranu odstraněných dat pro všechny chráněné položky.
  - Počkejte, než 14 dní do vypršení platnosti bezpečnostního uchování.
  - Zakáže obnovitelné odstranění.

Chcete-li zakázat obnovitelné odstranění, zajistěte splnění požadavků a pak postupujte podle těchto kroků:

1. V Azure Portal přejdete do svého trezoru a pak přejdete na **nastavení** -> **vlastnosti**.
2. V podokně Vlastnosti vyberte **nastavení zabezpečení** -> **aktualizovat**.
3. V podokně nastavení zabezpečení v části obnovitelné odstranění vyberte **Zakázat**.

![Zakázat obnovitelné odstranění](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="other-security-features"></a>Další funkce zabezpečení

### <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Azure Storage automaticky šifruje vaše data při jejich uchování do cloudu. Šifrování chrání vaše data a usnadňuje splnění závazků týkajících se zabezpečení a dodržování předpisů v organizaci. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 šifrování AES, je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru. Azure Backup data před uložením automaticky šifrují. Před načtením Azure Storage data dešifruje.  

V rámci Azure jsou data přenášená mezi službou Azure Storage a trezorem chráněná protokolem HTTPS. Tato data zůstávají v páteřní síti Azure.

Další informace najdete v [Azure Storage šifrování pro](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)neaktivní neaktivní data.

### <a name="vm-encryption"></a>Šifrování virtuálního počítače

Pomocí služby Azure Backup můžete zálohovat a obnovovat virtuální počítače Azure s Windows nebo Linuxem pomocí šifrovaných disků. Pokyny najdete v tématu [zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Ochrana Azure Backup bodů obnovení

Účty úložiště používané trezory služby Recovery Services jsou izolované a uživatelé k nim nemají k dispozici žádné škodlivé účely. Přístup je povolený jenom prostřednictvím operací správy Azure Backup, jako je například obnovení. Tyto operace správy se řídí pomocí Access Control na základě rolí (RBAC).

Další informace najdete v tématu [použití Access Control na základě rolí ke správě Azure Backup bodů obnovení](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="soft-delete"></a>Obnovitelné odstranění

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Musím u každého trezoru povolit funkci obnovitelného odstranění?

Ne, ve výchozím nastavení je pro všechny trezory služby Recovery Services postavené a povolené.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Můžu nakonfigurovat počet dní, po které se po dokončení operace odstranění budou uchovávat data ve stavu tichého odstranění?

Ne, je po operaci odstranění pevně nastavená na 14 dní dalšího uchování.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Musím platit náklady za další 14 dní v tomto případě?

Ne, toto 14. další uchovávání dat se zaznamená zdarma jako součást funkcí obnovitelného odstranění.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Můžu provést operaci obnovení, když jsou moje data ve stavu tichého odstranění?

Ne, chcete-li obnovit, je nutné obnovit neodstraněný prostředek. Operace zrušení odstranění vrátí prostředek zpátky do **stavu zastavit ochranu se stavem zachovat data** , kde se můžete kdykoli vrátit k libovolnému bodu v čase. Systém uvolňování paměti zůstává v tomto stavu pozastaven.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Budou se moje snímky řídit stejným životním cyklem jako body obnovení v trezoru?

Ano.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Jak můžu znovu aktivovat naplánované zálohy u prostředku odstraněného z nějakého obnovitelného?

Operace zrušit odstranění, po které následuje operace pokračovat, bude chránit prostředek znovu. Operace obnovení přidruží zásadu zálohování, která spustí plánované zálohy s vybranou dobou uchovávání. Systém uvolňování paměti se také spouští ihned po dokončení operace obnovení. Pokud chcete provést obnovení z bodu obnovení, který je po datu vypršení platnosti, doporučujeme to provést před aktivací operace obnovení.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Můžu odstranit svůj trezor, pokud jsou v trezoru obnovitelné odstraněné položky?

Trezor Recovery Services nelze odstranit, pokud jsou v trezoru nějaké zálohované položky. Odstraněné položky jsou po 14 dnech operace odstranění trvale odstraněny. Trezor můžete odstranit až po vyprázdnění všech neodstraněných položek.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Můžu odstranit data starší než 14 dní, po jejichž uplynutí bude období obnovitelného odstranění?

Ne. Nelze odstranit dočasně odstraněné položky, budou automaticky odstraněny po 14 dnech. Tato funkce zabezpečení má povolenou ochranu zálohovaných dat před náhodnými nebo škodlivými odstraněními.  Měli byste počkat na 14 dní před provedením jakékoli jiné akce na virtuálním počítači.  Odstraněné položky se nebudou účtovat.  Pokud potřebujete znovu ochránit virtuální počítače označené pro obnovitelné odstranění během 14 dní do nového trezoru, obraťte se na podporu Microsoftu.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Může dojít k obnovitelnému odstranění operací v PowerShellu nebo rozhraní příkazového řádku?

Ne, podpora PowerShellu nebo rozhraní příkazového řádku není aktuálně k dispozici.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Podporuje se obnovitelné odstranění pro jiné cloudové úlohy, jako je SQL Server ve virtuálních počítačích Azure a SAP HANA na virtuálních počítačích Azure?

Ne. V současné době je obnovitelné odstranění podporováno pouze pro virtuální počítače Azure.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [ovládacích prvcích zabezpečení pro Azure Backup](backup-security-controls.md).
