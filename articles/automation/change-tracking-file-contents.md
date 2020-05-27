---
title: Správa Change Tracking a inventáře v Azure Automation
description: V tomto článku se dozvíte, jak pomocí Change Tracking a inventáře sledovat změny softwaru a služeb Microsoftu ve vašem prostředí.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 4b8bf6a3f583e4c17f61e0a46911990ac5cc827c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830476"
---
# <a name="manage-change-tracking-and-inventory"></a>Správa řešení Change Tracking a Inventory

Azure Automation povoluje funkci [Change Tracking a inventáře](change-tracking.md) pro počítače ve vašem prostředí. Funkce sleduje a zpřístupňuje dostupné změny v klíčích registru, souborech, obsahech a podobně. Tento článek obsahuje postupy pro práci s touto funkcí.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Povolení úplné funkce Change Tracking a inventáře

Pokud jste povolili [Azure Security Center Monitoring integrity souborů (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), můžete pro počítače použít úplnou funkci Change Tracking a inventáře, jak je popsáno níže. Vaše nastavení tento proces neodstraní.

> [!NOTE]
> Povolení úplných funkcí Change Tracking a inventáře může způsobit další poplatky. Podívejte se na téma [ceny služby Automation](https://azure.microsoft.com/pricing/details/automation/).

1. Odstraňte řešení monitorování tak, že přejdete do pracovního prostoru a vyhledáte ho v [seznamu nainstalovaných řešení monitorování](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Kliknutím na název řešení otevřete jeho stránku Shrnutí a pak klikněte na **Odstranit**, jak je popsáno v části [Odebrání řešení monitorování](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Pokud chcete znovu povolit Change Tracking a inventáře, přejděte do účtu Automation a v části **Správa konfigurace**vyberte **změnit sledování** nebo **inventář** .
4. Vyberte pracovní prostor Log Analytics a účet Automation, potvrďte nastavení pracovního prostoru a klikněte na **Povolit**.

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>Povolit počítače pro Change Tracking a inventář

Chcete-li začít sledovat změny, je nutné povolit Change Tracking a inventář v Azure Automation. Tady jsou doporučené a podporované způsoby povolení této funkce pro vaše počítače: 

* [Povolit z virtuálního počítače](automation-onboard-solutions-from-vm.md)
* [Povolit z prohlížení více počítačů](automation-onboard-solutions-from-browse.md)
* [Povolení z účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [Povolení v sadě Azure Automation Runbook](automation-onboard-solutions.md)

## <a name="track-files"></a>Sledování souborů

### <a name="configure-file-tracking-on-windows"></a>Konfigurace sledování souborů ve Windows

Ke konfiguraci sledování souborů na počítačích se systémem Windows použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). 
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Na stránce Konfigurace pracovního prostoru vyberte **soubory Windows**a potom kliknutím na **+ Přidat** přidejte nový soubor, který chcete sledovat.
4. V podokně Přidat soubor Windows pro Change Tracking zadejte informace o souboru, který se má sledovat, a klikněte na **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | True, pokud je nastavení použito, a jinak false.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Skupina     | Název skupiny pro logicky seskupené soubory.        |
    |Zadat cestu     | Cesta pro kontrolu souboru, například **c:\Temp \\ \* . txt**. Můžete také použít proměnné prostředí, například `%winDir%\System32\\\*.*` .       |
    |Typ cesty     | Typ cesty Možné hodnoty jsou soubor a adresář.        |    
    |Rekurze     | True, pokud se používá rekurze při hledání položky, která se má sledovat, a v opačném případě false.        |    
    |Nahrát obsah souboru | True pro nahrání obsahu souboru při sledovaných změnách a v opačném případě false.|

5. Ujistěte se, že pro **nahrání obsahu souboru**zadáte hodnotu true. Toto nastavení povoluje sledování obsahu souborů pro určenou cestu k souboru.

### <a name="configure-file-tracking-on-linux"></a>Konfigurace sledování souborů v systému Linux

Ke konfiguraci sledování souborů na počítačích se systémem Linux použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). 
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Na stránce Konfigurace pracovního prostoru vyberte **soubory pro Linux**a potom kliknutím na **+ Přidat** přidejte nový soubor, který chcete sledovat.
4. V podokně Přidat soubor pro Linux pro Change Tracking zadejte informace o souboru nebo adresáři, který se má sledovat, a klikněte na **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | True, pokud je nastavení použito, a jinak false.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Skupina     | Název skupiny pro logicky seskupené soubory.        |
    |Zadat cestu     | Cesta pro kontrolu souboru, například **/etc/*. conf**.       |
    |Typ cesty     | Typ cesty Možné hodnoty jsou soubor a adresář.        |
    |Rekurze     | True, pokud se používá rekurze při hledání položky, která se má sledovat, a v opačném případě false.        |
    |Použít sudo     | True pro použití sudo při kontrole položky a v opačném případě false.         |
    |Odkazy     | Nastavení, které určuje, jak se má při procházení adresářů jednat o symbolické odkazy. Možné hodnoty:<br> Ignore-ignoruje symbolické odkazy a neobsahuje odkazované soubory/adresáře.<br>Sledovat – sleduje symbolické odkazy během rekurze a také obsahuje odkazované soubory/adresáře.<br>Spravovat – sleduje symbolické odkazy a umožňuje změnu vráceného obsahu. **Poznámka:** Tato možnost se nedoporučuje, protože nepodporuje načtení obsahu souborů.    |
    |Nahrát obsah souboru | True pro nahrání obsahu souboru při sledovaných změnách a v opačném případě false. |

5. Ujistěte se, že pro **nahrání obsahu souboru**zadáte hodnotu true. Toto nastavení povoluje sledování obsahu souborů pro určenou cestu k souboru.

   ![Přidat soubor pro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Sledovat obsah souboru

Sledování obsahu souborů umožňuje zobrazit obsah souboru před a po sledované změně. Tato funkce uloží obsah souboru do účtu úložiště poté, co dojde ke změně. Tady jsou některá pravidla, která se mají dodržovat při sledování obsahu souboru:

* Pro ukládání obsahu souboru se vyžaduje účet úložiště úrovně Standard, který používá model nasazení Správce prostředků. 

* Nepoužívejte účty úložiště modelu nasazení Premium a Classic. Přečtěte si informace [o Azure Storagech účtech](../storage/common/storage-create-storage-account.md).

* Účet úložiště, který používáte, se dá připojit jenom k jednomu účtu Automation.

* Ve vašem účtu Automation je povolený [Change Tracking a inventář](change-tracking.md) .

### <a name="enable-tracking-for-file-content-changes"></a>Povolit sledování pro změny obsahu souborů

1. V Azure Portal otevřete svůj účet Automation a potom v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ).
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Vyberte **soubor obsahu** a klikněte na **odkaz**. Tento výběr otevře podokno přidat umístění obsahu pro Change Tracking.

   ![Povolit umístění obsahu](./media/change-tracking-file-contents/enable.png)

4. Vyberte předplatné a účet úložiště, které chcete použít pro uložení obsahu souboru. 

5. Pokud chcete povolit sledování obsahu souborů pro všechny existující sledované soubory, vyberte **zapnuto** pro **nahrání obsahu souboru pro všechna nastavení**. Toto nastavení můžete pro každou cestu souboru změnit později.

   ![Nastavit účet úložiště](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking a inventář zobrazuje identifikátory URI účtu úložiště a identifikátorů URI sdíleného přístupového podpisu (SAS), pokud umožňuje sledování změn obsahu souboru. Platnost podpisů vyprší po 365 dnech a můžete je znovu vytvořit kliknutím na tlačítko **znovu vygenerovat**.

   ![Výpis klíčů účtu](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Zobrazení obsahu sledovaného souboru

Jakmile Change Tracking a inventář zjistí změnu pro sledovaný soubor, můžete zobrazit jeho obsah v podokně Podrobnosti o změně.  

![Zobrazit seznam změn](./media/change-tracking-file-contents/change-list.png)

1. V Azure Portal otevřete svůj účet Automation a potom v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ).

2. Zvolte soubor v seznamu změn a výběrem možnosti **Zobrazit změny obsahu souboru** zobrazte obsah souboru. V podokně změnit podrobnosti se zobrazí standardní informace o souboru před a po.

   ![Změnit podrobnosti](./media/change-tracking-file-contents/change-details.png)

3. Zobrazujete obsah souboru v zobrazení vedle sebe. Pokud chcete zobrazit vložené zobrazení změn, můžete vybrat možnost **inline** .

## <a name="track-registry-keys"></a>Sledovat klíče registru

Ke konfiguraci sledování klíčů registru v počítačích se systémem Windows použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). 
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Na stránce Konfigurace pracovního prostoru vyberte možnost **registr systému Windows**.
4. Kliknutím na **+ Přidat** přidejte nový klíč registru, který chcete sledovat.
5. V podokně Přidat registr systému Windows pro Change Tracking zadejte informace pro klíč, který chcete sledovat, a klikněte na **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | True, pokud je nastavení použito, a jinak false.        |
    |Název položky     | Popisný název klíče registru, který se má sledovat        |
    |Skupina     | Název skupiny pro logicky seskupené klíče registru.        |
    |Klíč registru systému Windows   | Název klíče s cestou, například **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user prostředí Folders\Common po spuštění**.      |

## <a name="search-logs-for-change-records"></a>V protokolech hledání záznamů změn

Můžete provádět různé vyhledávání v protokolech Azure Monitor pro záznamy změn. Otevřete stránku pro sledování změn, kliknutím na **Log Analytics** otevřete stránku protokoly. V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy změn.

|Dotaz  |Popis  |
|---------|---------|
|ConfigurationData<br>&#124; WHERE ConfigDataType = = "Microsoft Services" a SvcStartupType = = "auto"<br>&#124; WHERE SvcState = = "zastaveno"<br>&#124; sumarizace arg_max (TimeGenerated, *) podle software ComputerName, Computer         | Zobrazuje nejaktuálnější záznamy inventáře pro služby společnosti Microsoft, které byly nastaveny na hodnotu automaticky, ale byly hlášeny jako zastaveno. Výsledky jsou omezené na nejnovější záznam pro zadaný název softwaru a počítač.    |
|ConfigurationChange<br>&#124; WHERE ConfigChangeType = = "software" a ChangeCategory = = "odebráno"<br>Pořadí &#124; podle TimeGenerated DESC|Zobrazuje záznamy změn pro odebraný software.|

## <a name="create-alerts-on-changes"></a>Vytváření upozornění na změny

Následující příklad ukazuje, že soubor **C:\Windows\System32\drivers\etc\hosts** byl změněn v počítači. Tento soubor je důležitý, protože ho systém Windows používá k překladu názvů hostitelů na IP adresy. Tato operace má přednost před DNS a může způsobit problémy s připojením. Může také vést k přesměrování provozu na škodlivé nebo jinak nebezpečné weby.

![Graf zobrazující změnu souboru hostitelů](./media/change-tracking-file-contents/changes.png)

Pomocí tohoto příkladu se podíváme na postup, jak na změnu vytvořit výstrahy.

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte možnost **sledování změn** a pak vyberte **Log Analytics**. 
2. V hledání v protokolech vyhledejte změny obsahu v souboru **hosts** pomocí dotazu `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Tento dotaz vyhledá změnu obsahu pro soubory s plně kvalifikovanou cestou obsahující slovo "hostitelé". Můžete také požádat o konkrétní soubor tak, že změníte část cesty na jeho plně kvalifikovaný tvar, například pomocí `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Jakmile dotaz vrátí požadované výsledky, kliknutím na **nové pravidlo výstrahy** v hledání protokolu otevřete stránku pro vytvoření výstrahy. Na tuto stránku můžete přejít také pomocí **Azure monitor** v Azure Portal. 

4. Znovu ověřte dotaz a upravte logiku výstrah. V takovém případě budete chtít aktivovat výstrahu, pokud je ve všech počítačích v prostředí zjištěna i jedna změna.

    ![Změnit na dotaz pro sledování změn v souboru Hosts](./media/change-tracking-file-contents/change-query.png)

5. Po nastavení logiky výstrah přiřaďte skupiny akcí, aby prováděly akce v reakci na aktivované upozornění. V tomto případě nastavili jsme e-maily, které se mají odeslat, a vytvoří se lístek ITSM (IT Service Management), který se má vytvořit. 

    ![Konfigurace skupiny akcí pro upozornění na změnu](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Další kroky

* Pokud potřebujete hledat v protokolech uložených v pracovním prostoru Log Analytics, přečtěte si téma [prohledávání protokolů v](../log-analytics/log-analytics-log-searches.md)protokolech Azure monitor.
* Řešení chyb funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).