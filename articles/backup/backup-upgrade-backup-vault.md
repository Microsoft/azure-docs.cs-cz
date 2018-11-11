---
title: Upgrade úložiště záloh do trezoru služby Recovery Services, Azure Backup.
description: Upgrade úložiště záloh do trezoru služby Recovery Services se získat nové funkce, jako je zálohování virtuálních počítačů, vyšší míru zabezpečení, zálohování virtuálních počítačů VMware a zálohování stavu systému pro servery Windows Resource Manageru
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/10/2017
ms.author: trinadhk
ms.openlocfilehash: 01aacaecba8c5a4adf1dab5483a2f921df9314c0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252526"
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Úložiště záloh se upgradovalo na trezor služby Recovery Services
Tento článek obsahuje přehled o jaké trezoru služby Recovery Services poskytuje, nejčastější dotazy týkající se upgradu existující zálohy trezoru do trezoru služby Recovery Services a kroků po upgradu. Trezor služby Recovery Services je ekvivalentem Azure Resource Manageru z trezoru služby Backup, které jsou uloženy vaše zálohovaná data. Data je obvykle kopií dat, nebo informace o konfiguraci pro virtuální počítače (VM), úlohy, serverech nebo pracovních stanic, zda v místním prostředí nebo v Azure.

## <a name="what-is-a-recovery-services-vault"></a>Co je trezor služby Recovery Services?
Trezor služby Recovery Services je entita online úložiště v Azure, která slouží k uchovávání dat, jako jsou záložní kopie, body obnovení a zásady zálohování. Trezory služby Recovery Services můžete použít k uložení zálohovaných dat pro různé služby Azure, jako jsou virtuální počítače IaaS (Linux nebo Windows) a databáze Azure SQL. Obnovení služby trezory podporu systému System Center DPM, Windows Server, Azure Backup serveru a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Trezory Recovery Services porovnání služeb zotavení a trezory Backup
Trezory služby Recovery Services jsou založené na modelu Azure Resource Manageru v Azure, zatímco trezory služby Backup jsou založené na modelu Azure Service Manager. Při upgradu trezoru služby Backup na trezor služby Recovery Services záložní data zůstanou beze změny během a po procesu upgradu. Trezory služby Recovery Services poskytují funkce není k dispozici pro trezory služby Backup, jako například:

- **Rozšířené možnosti zabezpečení zálohování dat**: trezory Recovery Services pomocí služby Recovery Services, Azure Backup poskytuje funkce zabezpečení k ochraně cloudové zálohy. Tyto funkce zabezpečení zajistit, že můžete zabezpečit vaše zálohy a bezpečně obnovit data ze zálohy v cloudu i v případě, že dojde k ohrožení produkční a zálohování serverů. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaši hybridní IT prostředí**: trezory Recovery Services pomocí služby Recovery Services, můžete monitorovat nejenom vaše [virtuálních počítačů Azure IaaS](backup-azure-manage-vms.md) , ale také vaše [místní prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrální portál. [Další informace](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Řízení přístupu na základě rolí (RBAC)**: RBAC poskytuje jemně odstupňované řízení řízení přístupu v Azure. [Azure nabízí různé předdefinované role](../role-based-access-control/built-in-roles.md), a Azure Backup obsahuje tři [předdefinovaných rolí ke správě body obnovení](backup-rbac-rs-vault.md). Trezory služby Recovery Services, musí být kompatibilní s RBAC, který omezuje zálohování a obnovení přístupu k definovanou sadu rolí uživatelů. [Další informace](backup-rbac-rs-vault.md)

- **Chránit všechny konfigurace virtuálních počítačů Azure**: trezory služby Recovery Services ochrany založené na správci prostředků virtuálních počítačů, včetně disků Premium Managed Disks a šifrovaných virtuálních počítačů. Upgrade úložiště záloh do trezoru služby Recovery Services vám dává příležitost k upgradu vašich virtuálních počítačů založené na portálu Service Manager na virtuální počítače založené na Resource Manageru. Při upgradu trezoru, můžete zachovat body obnovení virtuálního počítače založené na portálu Service Manager a nakonfigurujte ochranu pro upgradovaný virtuální počítače (Resource Manager s podporou). [Další informace](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Okamžitá obnova pro virtuální počítače IaaS**: trezory Recovery Services pomocí služby Recovery Services, můžete obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celého virtuálního počítače, který umožňuje rychlejší obnovení. Okamžitá obnova pro virtuální počítače IaaS je k dispozici pro virtuální počítače s Linuxem i Windows. [Další informace](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Pokud máte položky zaregistrované do trezoru služby Backup pomocí agenta MARS starší než 2.0.9083.0, [stáhnout nejnovější verzi agenta MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) verzi, aby mohli výhody všechny funkce trezoru služby Recovery Services. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Správa vašich trezorech služby Recovery Services
Na následující obrazovce zobrazit nový trezor služby Recovery Services, upgradovali z trezoru služby Backup na webu Azure Portal. Upgradovaný trezor bude k dispozici ve výchozí skupině prostředků s názvem "Výchozí RecoveryServices-ResourceGroup geografické". Příklad: Pokud trezoru služby Backup se nachází v oblasti západní USA, se zařadí se ve výchozím nastavení s názvem výchozí RecoveryServices ResourceGroup westus RG.
> [!NOTE]
> Pro zákazníky, kteří CPS Standard skupina prostředků se nezmění po upgradu trezoru a zůstává stejná jako před upgradem.

První obrazovka ukazuje řídicím panelu trezoru, který zobrazuje pro trezor klíčů entit.
![Příklad trezoru služby Recovery Services upgradovali z trezoru služby Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Druhá obrazovka ukazuje Nápověda odkazy vám pomůžou začít s pomocí trezoru služby Recovery Services.

![odkazy nápovědy v okně s rychlým startem](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Kroky po upgradu
Trezor služby Recovery Services umožňuje zadat informace o časovém pásmu zásady zálohování. Po úspěšném upgradu trezoru přejděte na zásady zálohování z nabídky Nastavení trezoru a aktualizujte informace o časovém pásmu pro každé ze zásad, které jsou nakonfigurované v trezoru. Tato obrazovka ukazuje již časovému plánu zálohování na místní časové pásmo použita při vytváření zásad. 

## <a name="enhanced-security"></a>Rozšířené zabezpečení
Při upgradu trezoru služby Backup na trezor služby Recovery Services, jsou nastavení zabezpečení pro tento trezor automaticky zapnuté. Při nastavení zabezpečení se na určité operace, jako je například odstranění zálohy, nebo změna přístupového hesla vyžadovat [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN kód. Další informace o rozšířené zabezpečení, najdete v článku [funkcí zabezpečení, ochraně hybridní zálohy](backup-azure-security-feature.md). Pokud je zapnuté rozšířené zabezpečení, data se uchovávají až do 14 dnů po informace o bodu obnovení se odstranil z trezoru. Zákazníkům se účtuje úložiště pro tato data zabezpečení. Uchovávání dat zabezpečení platí pro body obnovení pro agenta Azure Backup, Azure Backup Server a System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Shromažďování dat pro váš trezoru
Jednou budete upgrade na trezor služby Recovery Services, konfigurace sestav Azure Backup (pro virtuální počítače IaaS a služby Microsoft Azure Recovery Services agent) a přístupu k sestavy pomocí Power BI. Další informace o shromažďování dat, najdete v článku, [konfigurace služby Azure Backup sestavy](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Funkce upgradu plán ovlivní Můj probíhající zálohování?**</br>
Ne. Probíhající zálohování pokračovat bez přerušení, během a po provedení upgradu.

**Co dělá to upgradu znamená pro stávající nástroje?**</br>
Do modelu nasazení Resource Manageru k zajištění, že i nadále fungovat po upgradu je nutné aktualizovat stávající automatizaci nebo nástroje. Najdete odkazy na rutiny Powershellu pro [modelu nasazení Resource Manager](backup-client-automation.md).

**Je možné vrátit zpět po upgradu?**</br>
Ne. Po úspěšném upgradu prostředků se nepodporuje vrácení zpět.

**Můžete zobrazit Moje classic po upgradu trezoru?**</br>
Ne. Nelze zobrazit ani spravovat klasické trezoru po upgradu. Pouze budete moct používat nový portál Azure portal pro všechny akce správy v trezoru.

**Proč nelze zobrazit serverů chráněných pomocí agenta MARS v mé upgradovaný trezor?**</br>
Je potřeba nainstalovat nejnovější verzi agenta MARS zobrazíte všechny servery chráněné službou agenta MARS v trezoru. Můžete si stáhnout nejnovější verzi agenta z [tady]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Nejde zobrazit zásady zálohování pro servery chráněné nástrojem agenta MARS po upgradu**</br>
Zásady zálohování trezoru mohou být zastaralá. a proto nelze synchronizovat upgradovaný trezor. Aktualizujte zásady tak, aby Ujistěte se, že bude pořád zobrazovat vaše zásady v upgradovaný trezor.
Pokud chcete aktualizovat zásady, přejděte na agenta MARS a aktualizujte nakonfigurované zásady zálohování.

**Proč nelze aktualizovat mé zásady zálohování po upgradu?**</br>
To se stane, když jsou staré záložní agenta a vyberte období minimální doby uchování menší než minimální povolená hodnota. Při upgradu trezoru služby Backup na trezor služby Recovery Services, jsou nastavení zabezpečení pro tento trezor automaticky zapnuté. Aby se zajistilo, že nejsou vždy platný počet bodů obnovení k dispozici, se některé minimální období, které potřebujete zachovat podle funkce zabezpečení. Další podrobnosti najdete v [tady](backup-azure-security-feature.md).
Potřebujete také aktualizujte agenty Azure Backup na nejnovější verzi, aby mohli výhod nejnovějších funkcích Azure Backup.

**Aktualizoval(a) jsem agent, ale stále nemůže zobrazit všechny objekty synchronizuje dokonce dnů po upgradu**</br>
Zkontrolujte prosím, pokud jste se zaregistrovali stejném počítači pro více trezorů. Ujistěte se, že máte před sebou stejného trezoru, ke které je zaregistrovaný agenta MARS. Pokud chcete zjistit, které trezor agenta MARS je zaregistrovaná a může, otevřete registr Windows a zkontrolujte hodnotu klíče ServiceResourceName pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config v trezoru zaregistrované, zobrazí se agenta MARS existuje. Pokud není klíč ServiceResourceName viditelné ve vašem systému, kontaktujte nás s hodnotou klíče ResourceId a MachineId pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config a pomůžeme vám s řešením problému.

**Proč se nezobrazují informace úlohy pro moje prostředky po upgradu?**</br>
Monitorování záloh (agenta MARS a IaaS) je nová funkce, které získáte při upgradu trezoru služby Backup na trezor služby Recovery Services. Informacím o monitorování trvá až 12 hodin pro synchronizaci se službou.

**Jak můžu nahlásit problém?**</br>
Pokud se nezdaří libovolnou část upgrade trezoru Poznámka že OperationID uvedený v chybě. Microsoft Support bude fungovat proaktivně k vyřešení daného problému. Můžete se obrátili na podporu nebo pošlete nám e-mail na rsvaultupgrade@service.microsoft.com svým ID předplatného, název trezoru a ID operace. Pokusíme se problém vyřešit co nejrychleji. Pokud explicitně jste nedostali pokyny k tomu společnost Microsoft není zkuste operaci zopakovat.

## <a name="next-steps"></a>Další postup
Použijte v následujících článcích:</br>
[Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování Azure Backup serveru](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování Windows serveru](backup-configure-vault.md)
