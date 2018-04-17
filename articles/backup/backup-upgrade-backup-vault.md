---
title: Upgrade trezoru zálohování do trezoru služeb zotavení služby Azure Backup | Microsoft Docs
description: Upgrade trezoru zálohování do trezoru služeb zotavení získat nové funkce, například zálohování správce prostředků virtuálních počítačů, lepší zabezpečení, zálohování virtuálních počítačů VMware a zálohy stav systému pro systémy Windows Server
services: backup
documentationcenter: ''
author: trinadhk
manager: vijayts
editor: ''
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: 7c340f60bc648909d073821f1987036da9633458
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Trezor záloh upgradovat na trezor služeb zotavení
Tento článek obsahuje přehled jaké trezor služeb zotavení poskytuje, často kladené otázky týkající se upgradu existující zálohy trezoru do trezoru služeb zotavení a kroků po upgradu. Trezor služeb zotavení je ekvivalentem Azure Resource Manager, kde zálohovaných dat úložiště záloh. Data je obvykle kopie dat, nebo informace o konfiguraci pro virtuální počítače (VM), úlohy, servery nebo pracovní stanice, jestli místně nebo v Azure.

## <a name="what-is-a-recovery-services-vault"></a>Co je trezor služby Recovery Services?
Trezor služby Recovery Services je entita online úložiště v Azure, která slouží k uchovávání dat, jako jsou záložní kopie, body obnovení a zásady zálohování. Trezory služeb zotavení můžete použít pro uložení zálohy dat pro různé služby Azure, jako jsou virtuální počítače IaaS (Linux nebo Windows) a databází Azure SQL. Obnovení služby trezory podporu System Center DPM, Windows Server, Server pro zálohování Azure a další. Trezory služby Recovery Services usnadňují uspořádání dat záloh a současně minimalizují režii spojenou s jejich správou.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Trezory porovnáním různých služeb zotavení a trezory Backup
Trezory služeb zotavení jsou založené na modelu Azure Resource Manager ve službě Azure, zatímco trezory Backup jsou založené na modelu Azure Service Manager. Když upgradujete trezor Backup do trezoru služeb zotavení, zůstane beze změn zálohovaná data, během a po upgradu. Trezory služeb zotavení zadejte funkce není k dispozici pro trezory Backup, jako třeba:

- **Rozšířené možnosti zabezpečení zálohování dat**: trezory služeb zotavení s, Azure Backup poskytuje funkce zabezpečení k ochraně zálohování do cloudu. Tyto funkce zabezpečení zajistěte, aby vám zabezpečit vaše zálohy a bezpečně obnovit data z cloudu záloh, i když jsou v ohrožení produkčního prostředí a zálohování serverů. [Další informace](backup-azure-security-feature.md)

- **Centrální monitorování pro vaše prostředí IT hybridní**: trezory služeb zotavení s, můžete monitorovat nejenom vaše [virtuální počítače Azure IaaS](backup-azure-manage-vms.md) , ale také vaše [místní prostředky](backup-azure-manage-windows-server.md#manage-backup-items) z centrální portálu. [Další informace](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Řízení přístupu na základě rolí (RBAC)**: RBAC poskytuje správu řízení podrobných přístupu v Azure. [Azure poskytuje různé integrované role](../role-based-access-control/built-in-roles.md), a zálohování Azure má tři [předdefinovaných rolí ke správě body obnovení](backup-rbac-rs-vault.md). Trezory služeb zotavení jsou kompatibilní s RBAC, který omezuje zálohování a obnovení přístupu k definovanou sadu rolí uživatele. [Další informace](backup-rbac-rs-vault.md)

- **Chránit všechny konfigurace virtuálních počítačů Azure**: trezory služeb zotavení ochrana založené na správci prostředků virtuálních počítačů včetně prémiové disky, spravované disky a virtuální počítače šifrovaná. Upgrade trezor Backup do trezoru služeb zotavení vám dává příležitost k upgradu virtuální počítače založené na portálu Service Manager na virtuální počítače na základě Resource Manager. Při upgradu trezoru, můžete zachovat body obnovení virtuálních počítačů na bázi portálu Service Manager a nakonfigurovat ochranu upgradovaný virtuálních počítačů (Resource Manager povoleno). [Další informace](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Rychlé obnovení pro virtuální počítače IaaS**: trezory služeb zotavení pomocí, můžete obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celý virtuální počítač, který umožňuje rychlejší obnovení. Rychlé obnovení pro virtuální počítače IaaS je k dispozici pro systém Windows a virtuální počítače s Linuxem. [Další informace](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Pokud máte položky zaregistrovat do trezoru zálohování s agentem MARS starší než 2.0.9083.0, [stáhnout nejnovější verzi agenta MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) verzi, aby mohli výhod všech funkcí nástroje trezor služeb zotavení. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Správa vašeho trezory služeb zotavení
Následující obrazovky zobrazit nový trezor služeb zotavení, upgradovali z úložiště záloh na portálu Azure. Upgradovaná trezor bude k dispozici v do výchozí skupiny prostředků s názvem "Výchozí-RecoveryServices-ResourceGroup-geo". Příklad: Pokud váš trezor zálohování se nachází v západní USA, ji budou se poskytuje ve výchozí RG s názvem výchozí RecoveryServices ResourceGroup westus.
> [!NOTE]
> Pro zákazníky, prohlášení CPS Standard skupina prostředků není změnit po upgradu trezoru a zůstane stejný, jako byl před upgradem.

První obrazovka ukazuje trezoru řídicí panel, který zobrazí klíče entity trezoru.
![Příklad trezor služeb zotavení upgradovali z úložiště záloh](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Druhý obrazovky zobrazuje v nápovědě, odkazy, které vám pomůžou začít používat Trezor služeb zotavení.

![odkazy nápovědy v okně Rychlý Start](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Kroků po upgradu
Trezor služeb zotavení podporuje zadání informace o časovém pásmu v zásady zálohování. Po úspěšném upgradu trezoru přejděte do zásady zálohování z nabídky Nastavení trezoru a aktualizovat informace o časovém pásmu pro každé ze zásad nakonfigurovaná v trezoru. Tato obrazovka ukazuje již časový plán zálohování, zadaný jako na místní časové pásmo použít při vytvoření zásady. 

## <a name="enhanced-security"></a>Rozšířené zabezpečení
Úložiště záloh upgradován do trezoru služeb zotavení, nastavení zabezpečení pro tento trezor jsou automaticky zapnuté. Při nastavení zabezpečení jsou u určité operace, třeba odstranění zálohy, nebo změna přístupového hesla o vyžadují [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) PIN kód. Další informace o lepší zabezpečení, najdete v článku [funkcí zabezpečení k ochraně hybridní zálohy](backup-azure-security-feature.md). Pokud je zapnuta lepší zabezpečení, mají být uchována data nahoru na 14 dnů po informace o bodu obnovení se odstranil z trezoru. Zákazníci se účtují úložiště těchto dat zabezpečení. Uchovávání dat zabezpečení platí pro body obnovení pro agenta Azure Backup, serveru Azure Backup a System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Shromažďování dat pro váš trezoru
Jednou budete upgradovat do trezoru služeb zotavení, konfigurace sestav pro zálohování Azure (pro virtuální počítače IaaS a služeb zotavení Microsoft Azure agent) a mít přístup k sestavám pomocí Power BI. Další informace o shromažďování dat, najdete v článku [konfigurace služby Azure Backup sestavy](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Vliv plán upgradu Moje probíhající zálohování?**</br>
Ne. Probíhající zálohování pokračovat bez přerušení během a po provedení upgradu.

**Jaké jsou znamená to upgradu pro moje existující nástrojů?**</br>
K modelu nasazení Resource Manager zajistit, že nadále fungovat po upgradu je nutné aktualizovat existující automatizace nebo nástrojů. Najdete odkazy na rutiny prostředí PowerShell pro [modelu nasazení Resource Manager](backup-client-automation.md).

**Můžete I vrátit po provedení upgradu?**</br>
Ne. Vrácení zpět není podporována po úspěšném upgradu prostředky.

**Můžete zobrazit Moje classic trezoru po upgradu?**</br>
Ne. Nelze zobrazit nebo spravovat trezoru classic po upgradu. Pouze bude moci používat nový portál Azure pro všechny akce správy v trezoru.

**Proč nevidím serverů chráněných pomocí agenta MARS v mé upgradovaný trezoru?**</br>
Musíte nainstalovat nejnovější verzi agenta MARS zobrazíte všechny servery, které jsou chráněny agenta MARS v trezoru. Můžete si stáhnout nejnovější verzi agenta z [zde]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Nelze zobrazit zásady zálohování pro servery chráněné nástrojem agenta MARS po upgradu**</br>
Zásady zálohování pro úložiště může být zastaralé a proto nelze synchronizovat, upgradovaná trezoru. Aktualizujte zásadu a zajistit tak, aby najdete v části zásad v úložišti upgradovaný.
Pokud chcete aktualizovat zásady, přejděte do agenta MARS a aktualizovat nakonfigurované zásady zálohování.

**Proč nelze aktualizovat Moje zásady zálohování po upgradu?**</br>
To se stane, když jsou na původním zálohování agenta a vyberte dobu uchování minimální být menší než minimální povolená hodnota. Úložiště záloh upgradován do trezoru služeb zotavení, nastavení zabezpečení pro tento trezor jsou automaticky zapnuté. K zajištění, že nejsou vždy platný počet bodů obnovení k dispozici, je po určité době minimální uchování je třeba zachovat podle funkce zabezpečení. Další podrobnosti najdete v části [zde](backup-azure-security-feature.md).
Také je potřeba aktualizovat agenty nástroje Azure Backup na nejnovější verzi, aby mohli výhody nejnovějších funkcí služby Azure Backup.

**Aktualizaci Moje agenta, ale stále nelze zobrazit všechny objekty se synchronizovat i několik dní, po upgradu**</br>
Zkontrolujte prosím, pokud jste si zaregistrovali stejný počítač více trezorů. Ujistěte se, že máte před sebou ke stejnému trezoru, na které je registrované agenta MARS. Pokud chcete zjistit, které trezoru agenta MARS je zaregistrován, otevřete registr systému Windows a zkontrolujte hodnotu pro klíč ServiceResourceName pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config trezoru zaregistrované, zobrazí se agenta MARS existuje. Pokud není klíč ServiceResourceName viditelné ve vašem systému, oslovení nám s hodnotou klíče ResourceId a MachineId pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config a jsme vám pomůže vyřešit problém.

**Proč nevidím informace úlohy pro moje prostředky po upgradu?**</br>
Monitorování pro zálohování (agenta MARS a IaaS) je nová funkce, kterou dostanete při upgradu trezoru služby Backup do trezoru služeb zotavení. Informace o monitorování trvá až 12 hodin pro synchronizaci se službou.

**Jak se nahlásit problém?**</br>
Pokud jakékoli její části trezoru upgrade selže, Všimněte si, že že OperationID uvedený v chybě. Microsoft Support bude fungovat proaktivně k vyřešení problému. Můžete oslovení podpory nebo e-mailu nás na adrese rsvaultupgrade@service.microsoft.com s ID odběru, název trezoru a OperationId. Pokusíme se problém vyřešit co nejrychleji. Pokud se na společnost Microsoft výslovně pokyn není opakujte operaci.

## <a name="next-steps"></a>Další postup
Použijte v následujících článcích:</br>
[Zálohování virtuálních počítačů IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování serveru Azure Backup](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování serveru Windows](backup-configure-vault.md)
