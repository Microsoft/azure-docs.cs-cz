---
title: Upgrade trezor Backup do trezoru služeb zotavení
description: Pokyny a informace o podpoře k upgradu trezoru služby Azure Backup do trezoru služeb zotavení.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: 551bc2aa4ff80feb3f28b5698e25dfd1b03dc870
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607333"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Upgrade trezor Backup do trezoru služeb zotavení

Tento článek vysvětluje postup upgradu trezor Backup do trezoru služeb zotavení. Proces upgradu nebude mít vliv na všechny úlohy spuštění zálohování a dojde ke ztrátě žádné zálohovaná data. Hlavních důvodů k upgradu trezor Backup do trezoru služeb zotavení:
- Všechny funkce úložiště záloh jsou uchovány v trezoru služeb zotavení.
- Trezory služeb zotavení mají více funkcí než trezory Backup, včetně: lepší zabezpečení, integrované monitorování, rychlejší zálohování a obnovení na úrovni položek.
- Spravujte položky zálohování z vylepšené a zjednodušená portálu.
- Nové funkce se vztahuje pouze na trezory služeb zotavení.

## <a name="impact-to-operations-during-upgrade"></a>Dopad operace během upgradu

Při upgradu trezor Backup do trezoru služeb zotavení, neexistuje žádný vliv na vaše data roviny operace. Všechny úlohy zálohování pokračovat, protože normální a všechny úlohy active obnovení pokračovat bez přerušení. Během upgradu operace správy zpoplatněná krátký výpadek a nelze chránit nové položky nebo vytvořte ad hoc úlohy zálohování. Úlohy obnovení pro virtuální počítače IaaS neběží během upgradu. Upgrade trezoru trvá během necelé hodiny. Po dokončení trezoru služeb zotavení nahrazuje trezoru služby Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Změny nástroj po upgradu a automatizace

Během přípravy infrastruktury v trezoru upgradu, musíte aktualizovat existující automatizace nebo nástrojů pro zajištění, že nadále fungovat po upgradu.
Najdete odkazy na rutiny prostředí PowerShell pro [modelu nasazení Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Před upgradem

Před provedením upgradu vaší trezory Backup na trezory služby zotavení, zkontrolujte následující problémy.

- **Verze agenta minimální**: Chcete-li upgradovat svůj trezor, ujistěte se, agenta nástroje Microsoft Azure Recovery Services (MARS) je minimálně verze 2.0.9083.0. Pokud agenta MARS je starší než 2.0.9083.0, aktualizujte před zahájením procesu upgradu agenta.
- **Model fakturace na základě instance**: trezory služby zotavení podporují pouze model fakturace na základě Instance. Pokud máte úložiště záloh, které používá starší model fakturace na základě úložiště, převeďte fakturační model během upgradu.
- **Žádné konfigurace zálohy průběžné operace**: během upgradu, přístup k rovině správy je omezený. Dokončete všechny roviny akce správy a poté spusťte upgrade.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Pomocí skriptů prostředí PowerShell k upgradu vaší trezory

Můžete použít skripty prostředí PowerShell k upgradu vaší trezory Backup trezory služeb zotavení. Zkontrolujte, že máte požadované součásti prostředí PowerShell pro spuštění aktualizace trezoru. Skripty prostředí PowerShell pro trezory Backup nefungují pro trezory služeb zotavení. Příprava prostředí pro upgrade trezory:

1. Instalace nebo upgrade [Windows Management Framework (WMF) na verzi 5](https://www.microsoft.com/download/details.aspx?id=50395) nebo vyšší.
2. [Nainstalovat Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Stažení [skript prostředí PowerShell](https://aka.ms/vaultupgradescript2) k upgradu vaší trezorů.

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Pomocí následujícího skriptu k upgradu vaší trezorů. Následující ukázkový skript obsahuje vysvětlení parametrů.

RecoveryServicesVaultUpgrade 1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **-umístění** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName** `<rgname>`

**ID předplatného** -číslo ID předplatného úložiště, která se upgraduje.<br/>
**VaultName** -název úložiště záloh, která se upgraduje.<br/>
**Umístění** -umístění úložiště probíhá upgrade.<br/>
**Typ prostředku** -použít BackupVault.<br/>
**TargetResourceGroupName** – vzhledem k tomu, že upgradujete trezoru do nasazení založené na správci prostředků, zadejte skupinu prostředků. Můžete použít existující skupinu prostředků nebo vytvořit tím, že poskytuje nový název. Pokud píšete název skupiny prostředků, můžete vytvořit novou skupinu prostředků. Další informace o skupinách prostředků, přečtěte si to [přehled o skupinách prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Názvy skupin prostředků nastavit omezení. Dodržujte pokyny; selhání k tomu by mohlo způsobit trezoru upgrady selhání.
>
>**Azure US Government** zákazníků je nutné nastavit prostředí tak, aby "AzureUSGovernment" při spouštění skriptu.
>**Azure Číně** zákazníků je nutné nastavit prostředí tak, aby "AzureChinaCloud" při spouštění skriptu.

Následující fragment kódu je příkladem jaké příkazu prostředí PowerShell by měl vypadat jako:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Můžete taky spustit skript bez parametrů a zobrazí se výzva k zadání vstupy pro všechny požadované parametry.

Skript prostředí PowerShell vás vyzve k zadání přihlašovacích údajů. Zadejte své přihlašovací údaje dvakrát: jednou pro účet portálu Service Manager a ještě jednou pro účet správce prostředků.

### <a name="pre-requisites-checking"></a>Předpoklady kontrola
Po zadání přihlašovacích údajů Azure, Azure kontroluje, zda vaše prostředí splňuje následující požadavky:

- **Verze agenta minimální** -trezory Backup trezory služeb zotavení upgradujete agenta MARS nejméně verze 2.0.9083.0. Pokud máte položky zaregistrovat do trezoru zálohování s agentem starší než 2.0.9083.0, selže kontrola předpokladů. Pokud selže kontrola předpokladů, aktualizujte agenta a pokusit o upgrade trezoru znovu. Můžete si stáhnout nejnovější verzi agenta z [ http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Úlohy konfigurace průběžné**: Pokud někdo konfiguruje úlohy pro úložiště záloh nastavit upgradovaná nebo registrace položku, selže kontrola předpokladů. Dokončení konfigurace, nebo dokončit registraci položku a pak spusťte proces upgradu trezoru.
- **Model fakturace na základě úložiště**: trezory služeb zotavení podporují model fakturace na základě Instance. Pokud spustíte upgrade úložiště na úložiště záloh, který používá model fakturace na základě úložiště, zobrazí se výzva k upgradu model fakturace společně s trezoru. Jinak můžete aktualizovat model fakturace první, a poté spusťte upgrade trezoru.
- Identifikujte skupinu prostředků pro trezor služeb zotavení. Abyste mohli využívat funkce nasazení Resource Manager, musíte umístit do trezoru služeb zotavení ve skupině prostředků. Pokud nevíte, které skupiny prostředků použít, zadejte název a proces upgradu pro vás vytvoří skupinu prostředků. Proces upgradu také přidruží trezoru novou skupinu prostředků.

Po dokončení procesu upgradu kontrola požadavky proces vás vyzve k spusťte upgrade trezoru. Jakmile potvrdíte, proces upgradu obvykle trvá přibližně 15-20 minut v závislosti na velikosti svůj trezor. Pokud máte velké trezoru, upgrade může trvat až 90 minut.

## <a name="managing-your-recovery-services-vaults"></a>Správa vašeho trezory služeb zotavení

Následující obrazovky zobrazit nový trezor služeb zotavení, upgradovali z úložiště záloh na portálu Azure. První obrazovka ukazuje trezoru řídicí panel, který zobrazí klíče entity trezoru.

![Příklad trezor služeb zotavení upgradovali z úložiště záloh](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Druhý obrazovky zobrazuje v nápovědě, odkazy, které vám pomůžou začít používat Trezor služeb zotavení.

![odkazy nápovědy v okně Rychlý Start](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Kroků po upgradu
Trezor služeb zotavení podporuje zadání informace o časovém pásmu v zásady zálohování. Po úspěšném upgradu trezoru přejděte do zásady zálohování z nabídky Nastavení trezoru a aktualizovat informace o časovém pásmu pro každé ze zásad nakonfigurovaná v trezoru. Tato obrazovka ukazuje již časový plán zálohování, zadaný jako na místní časové pásmo použít při vytvoření zásady. 

## <a name="enhanced-security"></a>Rozšířené zabezpečení

Úložiště záloh upgradován do trezoru služeb zotavení, nastavení zabezpečení pro tento trezor jsou automaticky zapnuté. Při nastavení zabezpečení jsou u určité operace, třeba odstranění zálohy, nebo změna přístupového hesla o vyžadují [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN kód. Další informace o lepší zabezpečení, najdete v článku [funkcí zabezpečení k ochraně hybridní zálohy](backup-azure-security-feature.md). 

Pokud je zapnuta lepší zabezpečení, mají být uchována data nahoru na 14 dnů po informace o bodu obnovení se odstranil z trezoru. Zákazníci se účtují úložiště těchto dat zabezpečení. Uchovávání dat zabezpečení platí pro body obnovení pro agenta Azure Backup, serveru Azure Backup a System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Shromažďování dat pro váš trezoru

Jakmile upgradujete do trezoru služeb zotavení, konfigurace sestav pro zálohování Azure (pro virtuální počítače IaaS a Microsoft Azure Recovery Services (MARS)) a mít přístup k sestavám pomocí Power BI. Další informace o shromažďování dat, najdete v článku [konfigurace služby Azure Backup sestavy](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Vliv plán upgradu Moje probíhající zálohování?**</br>
Ne. Probíhající zálohování pokračovat bez přerušení během a po provedení upgradu.

**Pokud nemáte plánem týkající se upgradu brzy, co se stane Moje trezory?**</br>
Vzhledem k tomu, že všechny nové funkce platí pouze pro trezory služeb zotavení, doporučujeme vám, k upgradu vaší trezorů. Od 1. září 2017 Microsoft zahájíte upgrade automaticky záloh na trezory služeb zotavení. Po listopadu 30,2017, jste již nemohou vytvářet trezory Backup pomocí prostředí PowerShell. Váš trezor lze automaticky upgradovat kdykoli mezi nimi. Společnost Microsoft doporučuje, že co nejdříve upgradovat svůj trezor.

**Jaké jsou znamená to upgradu pro moje existující nástrojů?**</br>
Aktualizujte vaše nástrojů k modelu nasazení Resource Manager. Obnovení služby, které byly vytvořeny trezory pro použití v modelu nasazení Resource Manager. Plánování modelu nasazení Resource Manager a monitorování účtů pro rozdíl ve vašem trezory je důležité. 

**Během upgradu je k dispozici mnoho výpadku?**</br>
To závisí na počtu prostředků, které se upgraduje. Pro menší nasazení (několik desítkami chráněné instance) by měl celou upgrade trvat méně než 20 minut. U větších nasazení má trvat maximálně jednu hodinu.

**Můžete I vrátit po provedení upgradu?**</br>
Ne. Vrácení zpět není podporována po úspěšném upgradu prostředky.

**Můžete ověřit Moje předplatné nebo prostředky, které chcete zobrazit, pokud jsou schopné upgrade?**</br>
Ano. Prvním krokem při upgradu ověří, zda jsou prostředky umožňující upgradu. V případě, že ověření předpoklady nezdaří, zobrazí se zprávy všechny z důvodů, upgrade nelze dokončit.

**Můžete upgradovat Moje úložiště záloh na základě CSP?**</br>
Ne. V současné době nelze upgradovat na základě CSP záloh. Přidáme podpora pro upgrade trezory Backup na základě CSP v další verze.

**Můžete zobrazit Moje classic trezoru po upgradu?**</br>
Ne. Nelze zobrazit nebo spravovat trezoru classic po upgradu. Pouze bude moci používat nový portál Azure pro všechny akce správy v trezoru.

**Moje upgrade se nezdařil, ale na počítač, který uchovávat agenta, které vyžadují aktualizaci, už neexistuje. Co dělat v takovém případě?**</br>
Pokud potřebujete pro použití úložiště, zálohování tohoto počítače pro dlouhodobé uchovávání a potom nebude možné upgradovat trezoru. V budoucích verzích přidáme podpora pro upgrade takové trezoru.
Pokud k uložení zálohy tento počítač již nepotřebujete, pak prosím zrušte registraci tento počítač z trezoru a opakujte upgrade.

**Proč nevidím informace úlohy pro moje prostředky po upgradu?**</br>
Monitorování pro zálohování (agenta MARS a IaaS) je nová funkce, kterou dostanete při upgradu trezoru služby Backup do trezoru služeb zotavení. Informace o monitorování trvá až 12 hodin pro synchronizaci se službou.

**Jak se nahlásit problém?**</br>
Pokud jakékoli její části trezoru upgrade selže, Všimněte si, že že OperationID uvedený v chybě. Microsoft Support bude fungovat proaktivně k vyřešení problému. Můžete oslovení podpory nebo e-mailu nás na adrese rsvaultupgrade@service.microsoft.com s ID odběru, název trezoru a OperationId. Pokusíme se problém vyřešit co nejrychleji. Pokud se na společnost Microsoft výslovně pokyn není opakujte operaci.


## <a name="next-steps"></a>Další postup
Použijte následující článek a:</br>
[Zálohování virtuálních počítačů IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování serveru Azure Backup](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování serveru Windows](backup-configure-vault.md).
