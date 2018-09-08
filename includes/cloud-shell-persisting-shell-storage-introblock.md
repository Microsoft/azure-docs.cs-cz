---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: 6055b70c7df2704a334b7f14c9365863ddafbd5a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164538"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Zachování souborů ve službě Azure Cloud Shell
Cloud Shell využívá Azure File storage pro uchovávání souborů napříč relacemi. Při počátečním spuštění Cloud Shell zobrazí výzvu k přidružení nové nebo existující sdílenou složku uchovávání souborů napříč relacemi.

> [!NOTE]
> Bash a PowerShell sdílet stejnou sdílenou složku. Pouze jedné sdílené můžou být spojené s automatické připojení ve službě Cloud Shell.

## <a name="create-new-storage"></a>Vytvoření nového úložiště

Když používáte základní nastavení a vyberte jenom předplatné, Cloud Shell vytvoří tři prostředky vaším jménem v podporované oblasti, která je vám nejblíže.:
* Skupina prostředků: `cloud-shell-storage-<region>`
* Účet úložiště: `cs<uniqueGuid>`
* Sdílené složky: `cs-<user>-<domain>-com-<uniqueGuid>`

![Nastavení předplatného](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Sdílené složky se připojí jako `clouddrive` ve vašich `$Home` adresáře. Jedná se o jednorázovou akci a sdílené automaticky připojí v dalších relacích. 

> [!NOTE]
> Pro zabezpečení každý uživatel by měl zřídit vlastní účet úložiště.  Pro řízení přístupu na základě role (RBAC) musí uživatelé měli přístup přispěvatele nebo výše na úložiště účtu úroveň.

Sdílené složky obsahuje také 5 GB obrázek, který je vytvořen které automaticky přenese data do vaší `$Home` adresáře. To platí pro Bash i PowerShell.

## <a name="use-existing-resources"></a>Použít existující prostředky

Pomocí pokročilé možnosti můžete přidružit existující prostředky. Po zobrazení výzvy se nastavení úložiště, vyberte **zobrazit pokročilé nastavení** zobrazíte další možnosti. Filtr možností mají údaj vyplněný úložiště pro místně redundantní úložiště (LRS), geograficky redundantní úložiště (GRS) a účty zónově redundantní úložiště (ZRS). Přejděte [tady Další](https://docs.microsoft.com/azure/storage/common/storage-redundancy#choosing-a-replication-option) o možnostech replikace pro účty Azure Storage.

![Nastavení skupiny prostředků](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

Při výběru oblasti Cloud Shell je nutné vybrat připojení účtu úložiště zálohování v této oblasti také.

### <a name="supported-storage-regions"></a>Oblasti podporované služby storage
Související služby Azure storage, účtů se musí nacházet ve stejné oblasti jako počítač Cloud Shell, že jste jim připojení. Najít vaše aktuální oblasti může spustit `env` v prostředí Bash a vyhledejte proměnnou `ACC_LOCATION`. Sdílené složky zobrazí 5 GB image vytvořené pro vás k uchování vašich `$Home` adresáře.

Cloudové prostředí počítače existují v těchto oblastech:
|Oblast|Oblast|
|---|---|
|Amerika|USA – východ, střed USA – Jih, USA – západ|
|Evropa|Severní Evropa, Západní Evropa|
|Asie a Tichomoří|Střed, jihovýchodní Asie Indie|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Omezit vytváření prostředků pomocí zásad prostředků Azure
Účty úložiště, které vytvoříte ve službě Cloud Shell se označují `ms-resource-usage:azure-cloud-shell`. Pokud chcete zakázat uživatelům ve vytváření účtů úložiště ve službě Cloud Shell, vytvořte [zásadě prostředků Azure pro značky](../articles/azure-policy/json-samples.md) , které jsou aktivovány tuto konkrétní značku.
