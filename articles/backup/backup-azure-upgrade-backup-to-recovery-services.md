---
title: Upgrade trezoru služby Backup na trezor služby Recovery Services
description: Pokyny a informace o podpoře pro upgrade trezoru služby Azure Backup na trezor služby Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: 41a826304af338814666e80dfaf584021809dbb0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52880042"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Upgrade trezoru služby Backup na trezor služby Recovery Services

Tento článek vysvětluje postup upgradu trezoru služby Backup na trezor služby Recovery Services. Proces upgradu nebude mít vliv na všechny probíhající úlohy zálohování a zálohování se neztratí. Hlavní důvody k upgradu trezoru služby Backup na trezor služby Recovery Services:
- Všechny funkce trezoru služby Backup jsou zachovány v trezoru služby Recovery Services.
- Trezory služby Recovery Services mají víc funkcí než trezory služby Backup, včetně: lepší způsoby zabezpečení, integrované monitorování, rychlejší zálohování a obnovení na úrovni položky.
- Vylepšená, zjednodušené portálu spravujte zálohované položky.
- Nové funkce platí jenom pro trezory služby Recovery Services.

## <a name="impact-to-operations-during-upgrade"></a>Dopad na operace během upgradu

Při upgradu trezoru služby Backup na trezor služby Recovery Services, neexistuje žádný vliv na vaše operace roviny dat. Všechny úlohy zálohování pokračovat jako za normálních okolností a všechny aktivní obnovení úlohy se budou dál bez přerušení. Během upgradu krátké výpadky se vám účtovat operace správy, a nemůžete chránit nové položky nebo vytvářet úlohy zálohování ad hoc. Úlohy obnovení pro virtuální počítače IaaS neběží během upgradu. Upgrade trezoru trvá méně než hodinu. Jakmile budete hotovi, trezor služby Recovery Services nahradí trezoru služby Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Změny automatizace a nástrojů po upgradu

Při přípravě vaší infrastruktury pro upgrade trezoru, je nutné aktualizovat stávající automatizaci nebo nástroje, které Ujistěte se, že i nadále fungovat po upgradu.
Najdete odkazy na rutiny Powershellu pro [modelu nasazení Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Před upgradem

Před provedením upgradu trezorů služby Backup na trezory služby Recovery, zkontrolujte následující problémy.

- **Minimální verze agenta**: upgrade trezoru, ujistěte se, že agent Microsoft Azure Recovery Services (MARS) je minimálně verze 2.0.9083.0. Pokud agenta MARS je starší než 2.0.9083.0, aktualizujte před zahájením procesu upgradu agenta.
- **Model fakturace založený na instancích**: trezory služby Recovery Service podporují jenom model fakturace založený na instancích. Pokud máte úložiště záloh, která používá starší model fakturace založený na úložiště, převeďte model fakturace během upgradu.
- **Žádné konfigurace zálohování probíhající operace**: během upgradu, je omezen přístup k rovině správy. Dokončete všechny akce rovina správy a poté spusťte upgrade.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Pomocí skriptů prostředí PowerShell k upgradu svých trezorů

Můžete použít skripty prostředí PowerShell na trezory služby Backup upgradovat na trezory služby Recovery Services. Zkontrolujte, že máte požadované součásti prostředí PowerShell pro aktivaci upgrade trezoru. Skripty Powershellu pro trezory služby Backup nefungují pro trezory služby Recovery Services. Příprava prostředí pro upgradovat trezory služby:

1. Instalace nebo upgrade [Windows Management Frameworku (WMF) na verzi 5](https://www.microsoft.com/download/details.aspx?id=50395) nebo vyšší.
2. [Instalace Azure Powershellu MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Stáhněte si [skript prostředí PowerShell](https://aka.ms/vaultupgradescript2) k upgradu svých trezorů.

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu

Pomocí následujícího skriptu k upgradu svých trezorů. Následující ukázkový skript obsahuje vysvětlení parametrů.

RecoveryServicesVaultUpgrade 1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **– umístění** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName** `<rgname>`

**SubscriptionID** – identifikační číslo předplatné trezoru, který se upgraduje.<br/>
**VaultName** – název trezoru služby Backup, která se upgraduje.<br/>
**Umístění** – umístění probíhá upgrade trezoru.<br/>
**Element ResourceType** -použít Backupvaultu.<br/>
**TargetResourceGroupName** – protože provádíte upgrade trezoru nasazení využívající Resource Manager, zadejte skupinu prostředků. Můžete použít existující skupinu prostředků nebo vytvořte si ho zadáním nového názvu. Pokud budete mít překlep název skupiny prostředků, můžete vytvořit novou skupinu prostředků. Další informace o skupinách prostředků najdete v tomto [přehled o skupinách prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Názvy skupin prostředků mají omezení. Dodržujte pokyny; Pokud tak neučiníte by mohlo způsobit trezor upgradu k selhání.
>
>**Azure pro státní správu USA** zákazníci musí nastavit prostředí tak, aby "AzureUSGovernment" při spuštění skriptu.
>**Azure Čína** zákazníci musí nastavit prostředí tak, aby "AzureChinaCloud" při spuštění skriptu.

Následující fragment kódu je příkladem jaké váš příkaz prostředí PowerShell by měl vypadat takto:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Můžete také spustit skript bez parametrů a zobrazí se výzva k zadejte vstupy pro všechny požadované parametry.

Skript prostředí PowerShell vás vyzve k zadání přihlašovacích údajů. Zadejte svoje přihlašovací údaje dvakrát: jednou pro účet portálu Service Manager a podruhé pro účet správce prostředků.

### <a name="pre-requisites-checking"></a>Kontrola požadavků
Po zadání přihlašovacích údajů Azure, Azure ověří, že vaše prostředí splňuje následující požadavky:

- **Minimální verze agenta** – trezory služby Backup upgradovat na trezory služby Recovery Services vyžaduje agenta MARS na minimální verzi 2.0.9083.0. Pokud máte položky zaregistrované do trezoru služby Backup pomocí agenta starší než 2.0.9083.0 kontrolu požadovaných součástí se nezdaří. Pokud selže kontrola splnění předpokladů, aktualizujte agenta a zkuste upgrade trezoru znovu. Můžete si stáhnout nejnovější verzi agenta z [ http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Konfigurace úlohy probíhající**: Pokud někdo je konfigurace úlohy pro nastavení k upgradu trezoru služby Backup nebo zápis položky, selže kontrola splnění předpokladů. Dokončete konfiguraci, nebo ukončení registrace položky a potom zahájit upgrade trezoru.
- **Model fakturace založený na úložiště**: trezory služby Recovery Services podporují model fakturace založený na instancích. Pokud spustíte upgrade trezoru v trezoru služby Backup, která využívá model fakturace založený na úložiště, budete vyzváni k upgradu váš model fakturace spolu s trezoru. V opačném případě můžete aktualizovat váš model fakturace první, a poté spusťte upgrade trezoru.
- Identifikujte skupinu prostředků pro trezor služby Recovery Services. Abyste mohli využívat funkce nasazení Resource Manageru, je nutné umístit trezor služby Recovery Services ve skupině prostředků. Pokud si nejste jisti, kterou skupinu prostředků chcete použít, zadejte název a procesu upgradu pro vás vytvoří skupinu prostředků. Proces upgradu také přidruží k trezoru novou skupinu prostředků.

Po dokončení procesu upgradu kontroluje požadavky procesu vás vyzve k spusťte upgrade trezoru. Jakmile potvrdíte, proces upgradu obvykle trvá přibližně 15 až 20 minut, v závislosti na velikosti vašeho trezoru. Pokud máte velké úložiště, upgrade může trvat až 90 minut.

## <a name="managing-your-recovery-services-vaults"></a>Správa vašich trezorech služby Recovery Services

Na následující obrazovce zobrazit nový trezor služby Recovery Services, upgradovali z trezoru služby Backup na webu Azure Portal. První obrazovka ukazuje řídicím panelu trezoru, který zobrazuje pro trezor klíčů entit.

![Příklad trezoru služby Recovery Services upgradovali z trezoru služby Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Druhá obrazovka ukazuje Nápověda odkazy vám pomůžou začít s pomocí trezoru služby Recovery Services.

![odkazy nápovědy v okně s rychlým startem](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Kroky po upgradu
Trezor služby Recovery Services umožňuje zadat informace o časovém pásmu zásady zálohování. Po úspěšném upgradu trezoru přejděte na zásady zálohování z nabídky Nastavení trezoru a aktualizujte informace o časovém pásmu pro každé ze zásad, které jsou nakonfigurované v trezoru. Tato obrazovka ukazuje již časovému plánu zálohování na místní časové pásmo použita při vytváření zásad. 

## <a name="enhanced-security"></a>Rozšířené zabezpečení

Při upgradu trezoru služby Backup na trezor služby Recovery Services, jsou nastavení zabezpečení pro tento trezor automaticky zapnuté. Při nastavení zabezpečení se na určité operace, jako je například odstranění zálohy, nebo změna přístupového hesla vyžadovat [ověřování Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN kód. Další informace o rozšířené zabezpečení, najdete v článku [funkcí zabezpečení, ochraně hybridní zálohy](backup-azure-security-feature.md). 

Pokud je zapnuté rozšířené zabezpečení, data se uchovávají až do 14 dnů po informace o bodu obnovení se odstranil z trezoru. Zákazníkům se účtuje úložiště pro tato data zabezpečení. Uchovávání dat zabezpečení platí pro body obnovení pro agenta Azure Backup, Azure Backup Server a System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Shromažďování dat pro váš trezoru

Jakmile upgradujete na trezor služby Recovery Services, konfigurace sestav Azure Backup (pro virtuální počítače IaaS a Microsoft Azure Recovery Services (MARS)) a přístupu k sestavy pomocí Power BI. Další informace o shromažďování dat, najdete v článku, [konfigurace služby Azure Backup sestavy](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Funkce upgradu plán ovlivní Můj probíhající zálohování?**</br>
Ne. Probíhající zálohování pokračovat bez přerušení, během a po provedení upgradu.

**Pokud nechystám upgrade brzy, co se stane Moje trezorů?**</br>
Protože všechny nové funkce platí jenom pro trezory služby Recovery Services, doporučujeme vám upgradovat vaše trezory. Od 1. září 2017 Microsoft začne automaticky upgrade trezory služby backup na trezory služby Recovery Services. Po listopadu 30,2017, že se už nevytvářejí trezory služby Backup pomocí Powershellu. Váš trezor je možné automaticky upgradovat kdykoli mezi. Společnost Microsoft doporučuje že co nejdříve upgrade trezoru.

**Co dělá to upgradu znamená pro stávající nástroje?**</br>
Aktualizace nástrojů na model nasazení Resource Manager. V modelu nasazení Resource Manageru pomocí služby Recovery Services, které byly vytvořeny trezory. Plánování pro model nasazení Resource Manageru a účtování pro rozdíl ve svých trezorů, je důležité. 

**Během upgradu je velká Doba výpadku?**</br>
To závisí na počtu prostředků, které se upgraduje. Pro menší nasazení (několik desítek chráněné instance) zabere celý upgrade menší než 20 minut. U větších nasazení mělo by to trvat maximálně jednu hodinu.

**Je možné vrátit zpět po upgradu?**</br>
Ne. Po úspěšném upgradu prostředků se nepodporuje vrácení zpět.

**Můžete ověřit prostředky, které chcete zobrazit, pokud jsou schopné upgrade nebo předplatné?**</br>
Ano. Prvním krokem při upgradu ověří, zda jsou prostředky schopné upgrade. V případě selhání ověření požadovaných součástí, příjem zpráv pro všemi důvody, proč nelze dokončit upgrade.

**Můžete upgradovat Moje trezoru služby Backup na základě CSP?**</br>
Ne. V současné době nelze upgradovat na základě CSP trezory služby backup. Doplníme podporu pro upgrade trezory služby Backup na základě CSP v další vydané verze.

**Můžete zobrazit Moje classic po upgradu trezoru?**</br>
Ne. Nelze zobrazit ani spravovat klasické trezoru po upgradu. Pouze budete moct používat nový portál Azure portal pro všechny akce správy v trezoru.

**Tento upgrade se nezdařil, ale počítač, který se nachází agent vyžaduje aktualizaci se už neexistuje. Co dělat v takovém případě?**</br>
Pokud je potřeba použít úložiště, zálohy pro dlouhodobé uchovávání a potom tento počítač nebude možné upgrade trezoru. V budoucích verzích přidáme podporu pro upgrade taková úložiště.
Pokud k ukládání záloh z tohoto počítače už nepotřebujete, pak prosím zrušte registraci tento počítač z trezoru a zkuste upgrade zopakovat.

**Proč se nezobrazují informace úlohy pro moje prostředky po upgradu?**</br>
Monitorování záloh (agenta MARS a IaaS) je nová funkce, které získáte při upgradu trezoru služby Backup na trezor služby Recovery Services. Informacím o monitorování trvá až 12 hodin pro synchronizaci se službou.

**Jak můžu nahlásit problém?**</br>
Pokud se nezdaří libovolnou část upgrade trezoru Poznámka že OperationID uvedený v chybě. Microsoft Support bude fungovat proaktivně k vyřešení daného problému. Můžete se obrátili na podporu nebo pošlete nám e-mail na rsvaultupgrade@service.microsoft.com svým ID předplatného, název trezoru a ID operace. Pokusíme se problém vyřešit co nejrychleji. Pokud explicitně jste nedostali pokyny k tomu společnost Microsoft není zkuste operaci zopakovat.


## <a name="next-steps"></a>Další postup
Použijte v následujícím článku:</br>
[Zálohování virtuálního počítače s IaaS](backup-azure-arm-vms-prepare.md)</br>
[Zálohování Azure Backup serveru](backup-azure-microsoft-azure-backup.md)</br>
[Zálohování Windows serveru](backup-configure-vault.md).
