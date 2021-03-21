---
title: Hotpatch pro edici Windows serveru Azure (Preview)
description: Informace o tom, jak hotpatch edice Windows serveru Azure funguje a jak ji povolit
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 710e6902be6ebe28caaf40fb446e4ee7cd2bf4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687562"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch pro nové virtuální počítače (Preview)

Technologie HotPatching je nový způsob, jak nainstalovat aktualizace na nové virtuální počítače s Windows serverem Azure Edition (VM), které po instalaci nevyžadují restart. Tento článek obsahuje informace o hotpatch pro virtuální počítače edice Windows Server Azure, které mají následující výhody:
* Nižší dopad na úlohy s menším restartováním
* Rychlejší nasazení aktualizací, když jsou balíčky menší, je potřeba je nainstalovat rychleji a mít snazší orchestraci oprav pomocí Azure Update Manageru
* Lepší ochrana, protože balíčky aktualizací hotpatch jsou vymezeny na aktualizace zabezpečení systému Windows, které se instalují rychleji bez restartování

## <a name="how-hotpatch-works"></a>Jak funguje hotpatch

Hotpatch funguje tak, že nejdřív vytvoříte směrný plán s web Windows Update nejnovější kumulativní aktualizací. Hotpatches se pravidelně uvolňují (například v druhém úterý v měsíci), které se na tento směrný plán sestaví. Hotpatches bude obsahovat aktualizace, které nevyžadují restart. V pravidelných intervalech (počínaje každé tři měsíce) se směrný plán aktualizuje novou nejnovější kumulativní aktualizací.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch vzorový plán.":::

Existují dva typy směrných plánů: **plánované standardní hodnoty** a **neplánované standardní hodnoty**.
*  **Plánované standardní hodnoty** jsou vydávány v pravidelných tempo s hotpatch verzemi v.  Plánované standardní hodnoty zahrnují všechny aktualizace srovnatelné _nejnovější kumulativní aktualizace_ pro tento měsíc a vyžadují restart.
    * Výše uvedený plán vzorkování znázorňuje čtyři verze plánovaných standardních hodnot v kalendářním roce (pět souhrnů v diagramu) a osmi hotpatch verzí.
* **Neplánované standardní hodnoty** jsou vydané při vydání důležité aktualizace (jako je třeba nulová oprava) a tato konkrétní aktualizace se nedá uvolnit jako hotpatch.  Při vydání neplánovaných standardních hodnot se hotpatch verze nahradí neplánovaným směrným plánem v daném měsíci.  Neplánované standardní hodnoty také zahrnují všechny aktualizace v srovnatelné _nejnovější kumulativní aktualizaci_ pro daný měsíc a také vyžadují restart.
    * Výše uvedený plán vzorkování znázorňuje dva neplánované plány, které by nahradily verze hotpatch po měsících (skutečný počet neplánovaných standardních hodnot v roce není předem známý).

## <a name="regional-availability"></a>Regionální dostupnost
Hotpatch je k dispozici ve všech globálních oblastech Azure ve verzi Preview. Azure Government oblasti nejsou ve verzi Preview podporované.

## <a name="how-to-get-started"></a>Jak začít

> [!NOTE]
> Ve fázi Preview můžete začít jenom s Azure Portal pomocí [tohoto odkazu](https://aka.ms/AzureAutomanageHotPatch).

Pokud chcete na novém virtuálním počítači začít používat hotpatch, postupujte takto:
1.  Povolit přístup ve verzi Preview
    * U každého předplatného je vyžadováno povolení přístupu v jednom čase.
    * Přístup k verzi Preview lze povolit prostřednictvím rozhraní API, PowerShellu nebo rozhraní příkazového řádku, jak je popsáno v následující části.
1.  Vytvoření virtuálního počítače z Azure Portal
    * V rámci verze Preview budete muset začít s [tímto odkazem](https://aka.ms/AzureAutomanageHotPatch).
1.  Podrobnosti o poskytnutí virtuálního počítače
    * Ujistěte se, že je v rozevíracím seznamu Image vybraná možnost _Windows Server 2019 Datacenter: edice Azure._
    * V kroku karta Správa přejděte dolů k části aktualizace hostovaného operačního systému. Uvidíte, že je sada HotPatching nastavená na zapnuto a oprava se nastavila jako výchozí, do Azure – orchestrace oprav.
    * Možnost automaticky spravovat osvědčené postupy pro virtuální počítače budou ve výchozím nastavení povolené.
1. Vytvoření nového virtuálního počítače

## <a name="enabling-preview-access"></a>Povolení přístupu ve verzi Preview

### <a name="rest-api"></a>REST API

Následující příklad popisuje, jak povolit verzi Preview pro vaše předplatné:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí ```Register-AzProviderFeature``` rutiny povolte verzi Preview pro vaše předplatné.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Použijte ```az feature register``` k povolení verze Preview pro vaše předplatné.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Instalace opravy

V rámci verze Preview je [Automatické opravy hosta virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) povolené automaticky pro všechny virtuální počítače vytvořené pomocí _Windows serveru 2019 Datacenter: edice Azure_. Automatické opravy hosta virtuálního počítače jsou povolené:
* Opravy klasifikované jako kritické nebo zabezpečení se automaticky stáhnou a aplikují na virtuálním počítači.
* Opravy se aplikují v době mimo špičku v časovém pásmu virtuálního počítače.
* Orchestrace opravy se spravuje v Azure a opravy se používají po [principech dostupnosti – první](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching).
* Stav virtuálního počítače, jak je určený prostřednictvím signálů stavu platformy, se monitoruje, aby se zjistilo selhání oprav.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Jak funguje automatická oprava hosta virtuálního počítače?

Když je na virtuálním počítači povolená [Automatická oprava hosta virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) , stáhnou se a automaticky nasadí dostupné kritické aktualizace a opravy zabezpečení. Tento proces se při vydání nových oprav automaticky odstartuje každý měsíc. Vyhodnocení a instalace opravy jsou automatické a proces zahrnuje restartování virtuálního počítače podle potřeby.

S hotpatch povolenou na _Windows serveru 2019 Datacenter: virtuální počítače edice Azure_ , většina měsíčních aktualizací zabezpečení se doručuje jako hotpatches, které nevyžadují restartování. Nejnovější kumulativní aktualizace odeslané v měsících plánovaného nebo neplánovaného směrného plánu budou vyžadovat restartování virtuálního počítače. Další důležité aktualizace nebo opravy zabezpečení můžou být taky dostupné pravidelně, což může vyžadovat restartování virtuálního počítače.

Tento virtuální počítač se automaticky vyhodnocuje za několik dní a v rámci každých 30 dnů několikrát určí příslušné opravy pro daný virtuální počítač. Toto automatické posouzení zajišťuje, že všechny chybějící opravy budou zjištěny při nejbližší možné příležitosti.

Opravy se instalují do 30 dnů od měsíčních verzí oprav, podle [principů dostupnosti – první](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching). Opravy se instalují jenom v době mimo špičku pro virtuální počítač v závislosti na časovém pásmu virtuálního počítače. Aby se aktualizace nainstalovaly automaticky, musí být virtuální počítač spuštěný v době mimo špičku. Pokud je virtuální počítač vypnutý během pravidelného posouzení, vyhodnotí se a příslušné opravy se nainstalují automaticky během příštího pravidelného posouzení, když je virtuální počítač zapnutý. Další pravidelné hodnocení obvykle proběhne během několika dnů.

Aktualizace definic a další opravy, které nejsou klasifikovány jako kritické nebo zabezpečení, se neinstalují prostřednictvím automatických oprav hostů virtuálních počítačů.

## <a name="understanding-the-patch-status-for-your-vm"></a>Princip stavu opravy pro váš virtuální počítač

Pokud chcete zobrazit stav opravy pro váš virtuální počítač, přejděte do části **aktualizace hosta +** Host pro váš virtuální počítač v Azure Portal. V části **aktualizace operačního systému hosta** klikněte na přejít do hotpatch (Preview) a zobrazte nejnovější stav opravy pro váš virtuální počítač.

Na této obrazovce se zobrazí stav hotpatch pro váš virtuální počítač. Můžete si také projít, jestli máte k dispozici nějaké opravy pro váš virtuální počítač, který není nainstalovaný. Jak je popsáno výše v části "opravit instalaci", všechny aktualizace zabezpečení a kritické aktualizace se na VIRTUÁLNÍm počítači automaticky nainstalují pomocí [automatických oprav hostů na virtuálním](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) počítači a nevyžadují se žádné další akce. Opravy s jinými klasifikací aktualizací se neinstalují automaticky. Místo toho se dají zobrazit v seznamu dostupných oprav na kartě aktualizace dodržování předpisů. Historii nasazení aktualizací na VIRTUÁLNÍm počítači můžete zobrazit také prostřednictvím historie aktualizací. Zobrazí se historie aktualizací za posledních 30 dní spolu s podrobnostmi o instalaci opravy.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Správa hotpatch":::

S automatickými opravami hosta virtuálního počítače se váš virtuální počítač pravidelně a automaticky vyhodnocuje pro dostupné aktualizace. Tato pravidelná posouzení zajistí, že budou zjištěny dostupné opravy. Výsledky posouzení můžete zobrazit na obrazovce Aktualizace výše, včetně času posledního posouzení. Můžete také zvolit, že se má kdykoli aktivovat vyhodnocení opravy na vyžádání pro váš virtuální počítač, a to pomocí možnosti vyhodnotit nyní a po dokončení posouzení zkontrolovat výsledky.

Podobně jako u posouzení na vyžádání můžete nainstalovat opravy na vyžádání pro svůj virtuální počítač pomocí možnosti nainstalovat aktualizace nyní. Tady můžete zvolit instalaci všech aktualizací v části konkrétní klasifikace oprav. Můžete také zadat aktualizace, které chcete zahrnout nebo vyloučit, zadáním seznamu jednotlivých článků znalostní báze. Opravy nainstalované na vyžádání nejsou nainstalované pomocí zásad dostupnosti – First a můžou pro instalaci aktualizací vyžadovat další restartování a výpadky virtuálních počítačů.

## <a name="supported-updates"></a>Podporované aktualizace

Hotpatch pokrývá aktualizace zabezpečení systému Windows a udržuje paritu s obsahem aktualizací zabezpečení vydaných pro běžný kanál Windows Update (bez hotpatch).

Pro spuštění virtuálního počítače s Windows serverem Azure Edition s povoleným hotpatch je potřeba zvážit několik důležitých informací. K instalaci aktualizací, které nejsou součástí programu hotpatch, se stále vyžadují restartování. Restartování se také vyžaduje pravidelně po instalaci nového směrného plánu. Tato restartování udržují virtuální počítač v synchronizaci s nezabezpečenými opravami, které jsou součástí nejnovější kumulativní aktualizace.
* Opravy, které aktuálně nejsou součástí programu hotpatch, zahrnují aktualizace nesouvisející se zabezpečením vydané pro Windows a aktualizace pro jiné než Windows (například opravy .NET).  Tyto typy oprav je potřeba nainstalovat během měsíčního směrného plánu a budou vyžadovat restartování.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-is-hotpatching"></a>Co je technologie HotPatching?

* Technologie HotPatching je nový způsob, jak nainstalovat aktualizace na Windows Server 2019 Datacenter: virtuální počítač Azure Edition v Azure, který po instalaci nevyžaduje restart. Funguje tak, že opraví kód v paměti spuštěných procesů bez nutnosti restartování procesu.

### <a name="how-does-hotpatching-work"></a>Jak funguje technologie HotPatching?

* Technologie HotPatching funguje tak, že vytvoří Směrný plán s web Windows Update nejnovější kumulativní aktualizací a pak na základě tohoto směrného plánu sestaví aktualizace, které nevyžadují restart, aby se projevily.  Směrný plán se pravidelně aktualizuje novou kumulativní aktualizací. Kumulativní aktualizace zahrnuje všechny aktualizace zabezpečení a kvality a vyžaduje restart.

### <a name="why-should-i-use-hotpatch"></a>Proč mám používat hotpatch?

* Pokud používáte hotpatch na Windows serveru 2019 Datacenter: Azure Edition, váš virtuální počítač bude mít vyšší dostupnost (méně restartování) a rychlejší aktualizace (menší balíčky, které se nainstalují rychleji bez nutnosti restartovat procesy). Výsledkem tohoto procesu je virtuální počítač, který je vždy aktuální a zabezpečený.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Jaké typy aktualizací jsou pokryté hotpatch?

* Hotpatch v současné době pokrývá aktualizace zabezpečení systému Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Kdy se mi získá první aktualizace hotpatch?

* Hotpatch aktualizace jsou obvykle vydávány druhé úterý v měsíci. Další informace najdete níže.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Jak bude plán hotpatch vypadat jako?

* Technologie HotPatching funguje tak, že vytvoříte směrný plán s web Windows Update nejnovější kumulativní aktualizací a potom na tento směrný plán sestavíte hotpatch aktualizacemi vydaných měsíčně.  Během období Preview budou základní hodnoty vydány od každé tři měsíce. Viz následující obrázek pro příklad ročního měsíčního plánu (včetně neplánovaných standardních hodnot z důvodu nulových oprav).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch vzorový plán.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Jsou pořád potřeba restartování pro virtuální počítač zaregistrovaný v hotpatch?

* Restartování se ještě vyžaduje k instalaci aktualizací, které nejsou součástí programu hotpatch, a jsou nutné pravidelně po navýšení směrného plánu (web Windows Update nejnovější kumulativní aktualizace). Tento restart zajistí synchronizaci virtuálního počítače se všemi opravami zahrnutými v kumulativní aktualizaci. Směrné plány (které vyžadují restart) začnou na tempo tři měsíce a v průběhu času se zvýší.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Mají ovlivněné aplikace při instalaci aktualizace hotpatch?

* Vzhledem k tomu, že hotpatch opraví kód v paměti spuštěných procesů, aniž by bylo nutné proces restartovat, nebudou aplikace nijak ovlivněny procesem opravy. Všimněte si, že se liší od potenciálního dopadu na výkon a funkčnost samotné opravy.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Můžu na svém virtuálním počítači vypnout hotpatch?

* Hotpatch můžete na virtuálním počítači vypnout pomocí Azure Portal.  Vypnutím hotpatch zrušíte registraci virtuálního počítače z hotpatch, což vrátí virtuální počítač k typickému chování aktualizace pro Windows Server.  Jakmile zrušíte registraci z hotpatch na virtuálním počítači, můžete tento virtuální počítač znovu zaregistrovat, až se dopustí další hotpatch standardní hodnoty.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Můžu upgradovat z existujícího operačního systému Windows Server?

* Upgrade z existujících verzí Windows serveru (tj. edice Windows serveru 2016 nebo 2019) se momentálně nepodporuje. Upgrade na budoucí verze Windows Server Azure Edition se bude podporovat.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Můžu během verze Preview používat hotpatch pro produkční úlohy?

* Verze Preview jsou určené pouze pro účely testování a nikoli pro použití v produkčních prostředích.

### <a name="will-i-be-charged-during-the-preview"></a>Bude se mi účtovat během verze Preview?

* Licence pro Windows Server Azure Edition je ve verzi Preview zdarma. Náklady na podkladové infrastruktury nastavené pro váš virtuální počítač (úložiště, výpočetní prostředí, sítě atd.) se ale budou účtovat i v rámci vašeho předplatného.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Jak získám podporu pro řešení potíží s funkcí HotPatching?

* Můžete zasouborovat [lístek případu technické podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). U možnosti služba vyhledejte a vyberte **virtuální počítač s Windows** pod položkou Compute. Vyberte **funkce Azure** pro typ problému a **Automatické opravy hosta virtuálního počítače** pro podtyp problému.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o Azure Update Management [tady](https://docs.microsoft.com/azure/automation/update-management/overview).
* Další informace o automatických opravách hosta virtuálního počítače [najdete tady](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) .