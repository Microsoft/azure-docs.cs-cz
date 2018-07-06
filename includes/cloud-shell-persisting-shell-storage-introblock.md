# <a name="persist-files-in-azure-cloud-shell"></a>Zachování souborů ve službě Azure Cloud Shell
Cloud Shell využívá Azure File storage pro uchovávání souborů napříč relacemi.

## <a name="set-up-a-clouddrive-file-share"></a>Nastavení sdílené složky clouddrive
Při počátečním spuštění Cloud Shell zobrazí výzvu k přidružení nové nebo existující sdílenou složku uchovávání souborů napříč relacemi.

> [!NOTE]
> Bash a PowerShell sdílet stejnou sdílenou složku. Pouze jedné sdílené můžou být spojené s automatické připojení ve službě Cloud Shell.

### <a name="create-new-storage"></a>Vytvoření nového úložiště

Když používáte základní nastavení a vyberte jenom předplatné, Cloud Shell vytvoří tři prostředky vaším jménem v podporované oblasti, která je vám nejblíže.:
* Skupina prostředků: `cloud-shell-storage-<region>`
* Účet úložiště: `cs<uniqueGuid>`
* Sdílené složky: `cs-<user>-<domain>-com-<uniqueGuid>`

![Nastavení předplatného](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Sdílené složky se připojí jako `clouddrive` ve vašich `$Home` adresáře. Jedná se o jednorázovou akci a sdílené automaticky připojí v dalších relacích. 

> [!NOTE]
> Pro zabezpečení každý uživatel by měl zřídit vlastní účet úložiště.  Pro řízení přístupu na základě role (RBAC) musí uživatelé měli přístup přispěvatele nebo výše na úložiště účtu úroveň.

V prostředí Bash, sdílené složky obsahuje také 5 GB obrázek, který je vytvořen které automaticky přenese data ve vašich `$Home` adresáře. 

### <a name="use-existing-resources"></a>Použít existující prostředky

Pomocí pokročilé možnosti můžete přidružit existující prostředky. Po zobrazení výzvy se nastavení úložiště, vyberte **zobrazit pokročilé nastavení** zobrazíte další možnosti. Rozevírací nabídky se filtrují pro vaši oblast přiřazené službě Cloud Shell a místně redundantní úložiště a účtů geograficky redundantního úložiště.

Sdílené složky zobrazí 5 GB image vytvořené pro vás k uchování vašich `$Home` adresáře.

![Nastavení skupiny prostředků](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Omezit vytváření prostředků pomocí zásad prostředků Azure
Účty úložiště, které vytvoříte ve službě Cloud Shell se označují `ms-resource-usage:azure-cloud-shell`. Pokud chcete zakázat uživatelům ve vytváření účtů úložiště ve službě Cloud Shell, vytvořte [zásadě prostředků Azure pro značky](../articles/azure-policy/json-samples.md) , které jsou aktivovány tuto konkrétní značku.

## <a name="supported-storage-regions"></a>Oblasti podporované služby storage
Související služby Azure storage, účtů se musí nacházet ve stejné oblasti jako počítač Cloud Shell, že jste jim připojení.

Pokud chcete najít přiřazené oblasti můžete:
* V dialogovém okně "pokročilé úložiště nastavení" Zobrazit poznámky
* Odkaz na název účtu úložiště, které jsou pro vás vytvořili (např: `cloud-shell-storage-westus`)
* Spustit `env` a vyhledejte proměnnou `ACC_LOCATION`

Cloudové prostředí počítače existují v těchto oblastech:
|Oblast|Oblast|
|---|---|
|Amerika|USA – východ, střed USA – Jih, USA – západ|
|Evropa|Severní Evropa, Západní Evropa|
|Asie a Tichomoří|Střed, jihovýchodní Asie Indie|

