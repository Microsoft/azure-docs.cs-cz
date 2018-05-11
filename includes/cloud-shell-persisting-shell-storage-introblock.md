# <a name="persist-files-in-azure-cloud-shell"></a>Zachovat soubory v prostředí cloudu Azure
Cloudové prostředí využívá Azure File storage pro soubory zachová napříč relacemi.

## <a name="set-up-a-clouddrive-file-share"></a>Nastavení sdílené složky clouddrive
Při počáteční spuštění prostředí cloudu vás vyzve k přidružit nové nebo existující sdílenou složku souborů zachová napříč relacemi.

> [!NOTE]
> Bash a prostředí PowerShell sdílet stejné sdílené složky. Pouze jedné sdílené složce může být přidružen automatické připojování v prostředí cloudu.

### <a name="create-new-storage"></a>Vytvoření nového úložiště

Když používáte základní nastavení a vyberte pouze předplatné, cloudové prostředí vytvoří tři zdroje vaším jménem v podporované oblasti, které je nejblíže můžete:
* Skupina prostředků: `cloud-shell-storage-<region>`
* Účet úložiště: `cs<uniqueGuid>`
* Sdílené složky: `cs-<user>-<domain>-com-<uniqueGuid>`

![Nastavení předplatného](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Sdílené složky připojí jako `clouddrive` ve vaší `$Home` adresáře. Toto je jednorázová akce a sdílené složky automaticky připojí v následné relace. 

> [!NOTE]
> Pro zabezpečení každý uživatel by měl zřídit vlastní úložiště.  Pro řízení přístupu na základě role (RBAC), musí mít uživatelé přístup Přispěvatel nebo vyšší.

V Bash, sdílené složky také obsahuje bitovou kopii 5 GB, který je vytvořen který automaticky dál data ve vaší `$Home` adresáře. 

### <a name="use-existing-resources"></a>Používat existující prostředky

Pomocí pokročilé možnosti můžete přidružit existující prostředky. Po zobrazení výzvy se instalační program úložiště, vyberte **zobrazit upřesňující nastavení** zobrazíte další možnosti. Rozevíracích nabídek jsou filtrovány pro vaši oblast přiřazené cloudové prostředí a místně redundantního úložiště a účty geograficky redundantní úložiště.

V Bash, existující sdílené složky zobrazí bitovou kopii 5 GB, abyste mohli zachovat vaše `$Home` adresáře.

![Nastavení skupiny prostředků](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Omezit vytvoření prostředku zásadami prostředků Azure.
Účty úložiště, které vytvoříte v prostředí cloudu jsou označené `ms-resource-usage:azure-cloud-shell`. Pokud chcete zakázat uživatelům ve vytváření účtů úložiště v prostředí cloudu, vytvořte [zásad prostředků Azure pro značky](../articles/azure-policy/json-samples.md) jsou aktivovány tato konkrétní značka.

## <a name="supported-storage-regions"></a>Oblasti podporované úložiště
Související úložiště Azure, které účty se musí nacházet ve stejné oblasti jako počítač cloudové prostředí, které jste jim připojení.

Vyhledání vašeho přiřazené oblasti, které jste se:
* V dialogovém okně "rozšířené úložiště nastavení" Zobrazit Poznámka
* Odkaz na název účtu úložiště vytvořeném pro vás (např: `cloud-shell-storage-westus`)
* Spustit `env` a vyhledejte proměnné `ACC_LOCATION`

Počítač prostředí cloudu neexistuje v následujících oblastech:
|Plošný|Oblast|
|---|---|
|Amerika|Východ USA, střed USA – Jih, západ USA|
|Evropa|Severní Evropa, Západní Evropa|
|Asie a Tichomoří|Indie centrální, jihovýchodní Asie|

