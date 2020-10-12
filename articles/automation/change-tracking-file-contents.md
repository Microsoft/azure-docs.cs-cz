---
title: Správa Change Tracking a inventáře v Azure Automation
description: V tomto článku se dozvíte, jak pomocí Change Tracking a inventáře sledovat změny softwaru a služeb Microsoftu ve vašem prostředí.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185598"
---
# <a name="manage-change-tracking-and-inventory"></a>Správa řešení Change Tracking a Inventory

Když přidáte nový soubor nebo klíč registru, který se má sledovat, Azure Automation ho povolí pro [Change Tracking a inventář](change-tracking.md). Tento článek popisuje, jak nakonfigurovat sledování, kontrolovat výsledky sledování a zpracovávat výstrahy při zjištění změn.

Než začnete používat postupy v tomto článku, ujistěte se, že jste povolili Change Tracking a inventarizaci na virtuálních počítačích pomocí jednoho z následujících postupů:

* [Povolení řešení Change Tracking a Inventory z účtu Automation](automation-enable-changes-from-auto-acct.md)
* [Povolení Change Tracking a inventáře procházením Azure Portal](automation-enable-changes-from-browse.md)
* [Povolení řešení Change Tracking a Inventory z runbooku](automation-enable-changes-from-runbook.md)
* [Povolení řešení Change Tracking a Inventory z virtuálního počítače Azure](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Omezení rozsahu nasazení

Change Tracking a inventář používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, aby přijímaly změny. Další informace najdete v tématu [omezení rozsahu nasazení Change Tracking a inventáře](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Sledování souborů

Pomocí Change Tracking a inventáře můžete sledovat změny souborů a složek nebo adresářů. V této části se dozvíte, jak nakonfigurovat sledování souborů ve Windows a v systému Linux.

### <a name="configure-file-tracking-on-windows"></a>Konfigurace sledování souborů ve Windows

Ke konfiguraci sledování souborů na počítačích se systémem Windows použijte následující postup:

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). 
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Na stránce Konfigurace pracovního prostoru vyberte **soubory Windows**a potom kliknutím na **+ Přidat** přidejte nový soubor, který chcete sledovat.
4. V podokně Přidat soubor Windows pro Change Tracking zadejte informace pro soubor nebo složku, které chcete sledovat, a klikněte na **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | True, pokud je nastavení použito, a jinak false.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Group (Skupina)     | Název skupiny pro logicky seskupené soubory.        |
    |Zadat cestu     | Cesta pro kontrolu souboru, například **c:\Temp \\ \* . txt**. Můžete také použít proměnné prostředí, například `%winDir%\System32\\\*.*` .       |
    |Typ cesty     | Typ cesty Možné hodnoty jsou soubor a složka.        |    
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
    |Group (Skupina)     | Název skupiny pro logicky seskupené soubory.        |
    |Zadat cestu     | Cesta pro kontrolu souboru, například **/etc/*. conf**.       |
    |Typ cesty     | Typ cesty Možné hodnoty jsou soubor a adresář.        |
    |Rekurze     | True, pokud se používá rekurze při hledání položky, která se má sledovat, a v opačném případě false.        |
    |Použít sudo     | True pro použití sudo při kontrole položky a v opačném případě false.         |
    |Odkazy     | Nastavení, které určuje, jak se má při procházení adresářů jednat o symbolické odkazy. Možné hodnoty:<br> Ignore-ignoruje symbolické odkazy a neobsahuje odkazované soubory/adresáře.<br>Sledovat – sleduje symbolické odkazy během rekurze a také obsahuje odkazované soubory/adresáře.<br>Spravovat – sleduje symbolické odkazy a umožňuje změnu vráceného obsahu.<br>**Poznámka:** Možnost spravovat se nedoporučuje, protože nepodporuje načtení obsahu souboru.    |
    |Nahrát obsah souboru | True pro nahrání obsahu souboru při sledovaných změnách a v opačném případě false. |

5. Ujistěte se, že pro **nahrání obsahu souboru**zadáte hodnotu true. Toto nastavení povoluje sledování obsahu souborů pro určenou cestu k souboru.

   ![Přidat soubor pro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Sledovat obsah souboru

Sledování obsahu souborů umožňuje zobrazit obsah souboru před a po sledované změně. Tato funkce uloží obsah souboru do [účtu úložiště](../storage/common/storage-account-overview.md) poté, co dojde ke změně. Tady jsou některá pravidla, která se mají dodržovat při sledování obsahu souboru:

* Pro ukládání obsahu souboru se vyžaduje účet úložiště úrovně Standard, který používá model nasazení Správce prostředků. 
* Nepoužívejte účty úložiště modelu nasazení Premium a Classic. Přečtěte si informace [o Azure Storagech účtech](../storage/common/storage-account-create.md).
* Účet úložiště můžete připojit jenom k jednomu účtu Automation.
* [Change Tracking a inventář](change-tracking.md) musí být ve vašem účtu Automation povolené.

### <a name="enable-tracking-for-file-content-changes"></a>Povolit sledování pro změny obsahu souborů

Chcete-li povolit sledování změn obsahu souboru, použijte následující postup:

1. V Azure Portal otevřete svůj účet Automation a potom v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ).
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Vyberte **soubor obsahu** a klikněte na **odkaz**. Tento výběr otevře podokno přidat umístění obsahu pro Change Tracking.

   ![Přidat umístění obsahu](./media/change-tracking-file-contents/enable.png)

4. Vyberte předplatné a účet úložiště, které chcete použít pro uložení obsahu souboru. 

5. Pokud chcete povolit sledování obsahu souborů pro všechny existující sledované soubory, vyberte **zapnuto** pro **nahrání obsahu souboru pro všechna nastavení**. Toto nastavení můžete pro každou cestu souboru změnit později.

   ![Nastavit účet úložiště](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking a inventář zobrazuje identifikátory URI účtu úložiště a identifikátorů URI sdíleného přístupového podpisu (SAS), pokud umožňuje sledování změn obsahu souboru. Platnost podpisů vyprší po 365 dnech a můžete je znovu vytvořit kliknutím na tlačítko **znovu vygenerovat**.

   ![Výpis klíčů účtu](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Zobrazení obsahu sledovaného souboru

Jakmile Change Tracking a inventář zjistí změnu pro sledovaný soubor, můžete zobrazit jeho obsah v podokně Podrobnosti o změně.  

![Zobrazit seznam změn](./media/change-tracking-file-contents/change-list.png)

1. V Azure Portal otevřete svůj účet Automation a potom v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ).

2. Zvolte soubor v seznamu změn a výběrem možnosti **Zobrazit změny obsahu souboru** zobrazte obsah souboru. V podokně Podrobnosti o změně se zobrazí standardní informace o souboru před a po a za každou vlastnost.

   ![Změnit podrobnosti](./media/change-tracking-file-contents/change-details.png)

3. Zobrazujete obsah souboru v zobrazení vedle sebe. Pokud chcete zobrazit vložené zobrazení změn, můžete vybrat možnost **inline** .

## <a name="track-registry-keys"></a>Sledovat klíče registru

Ke konfiguraci sledování klíčů registru v počítačích se systémem Windows použijte následující postup:

1. V Azure Portal otevřete svůj účet Automation a potom v části **Správa konfigurace**vyberte **Change Tracking (sledování změn** ). 
2. Klikněte na **Upravit nastavení** (symbol ozubeného kolečka).
3. Na stránce Konfigurace pracovního prostoru vyberte možnost **registr systému Windows**.
4. Kliknutím na **+ Přidat** přidejte nový klíč registru, který chcete sledovat.
5. V podokně Přidat registr systému Windows pro Change Tracking zadejte informace pro klíč, který chcete sledovat, a klikněte na **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | True, pokud je nastavení použito, a jinak false.        |
    |Název položky     | Popisný název klíče registru, který se má sledovat        |
    |Group (Skupina)     | Název skupiny pro logicky seskupené klíče registru.        |
    |Klíč registru systému Windows   | Název klíče s cestou, například `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>V protokolech hledání záznamů změn

Můžete provádět různé vyhledávání v protokolech Azure Monitor pro záznamy změn. Otevřete stránku pro sledování změn, kliknutím na **Log Analytics** otevřete stránku protokoly. V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy změn.

|Dotaz  |Description  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Zobrazuje nejaktuálnější záznamy inventáře pro služby společnosti Microsoft, které byly nastaveny na hodnotu automaticky, ale byly hlášeny jako zastaveno. Výsledky jsou omezené na nejnovější záznam pro zadaný název softwaru a počítač.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Zobrazuje záznamy změn pro odebraný software.|

## <a name="create-alerts-on-changes"></a>Vytváření upozornění na změny

Následující příklad ukazuje, že soubor **c:\Windows\System32\drivers\etc\hosts** byl změněn v počítači. Tento soubor je důležitý, protože ho systém Windows používá k překladu názvů hostitelů na IP adresy. Tato operace má přednost před DNS a může způsobit problémy s připojením. Může také vést k přesměrování provozu na škodlivé nebo jinak nebezpečné weby.

![Graf znázorňující změnu souboru hostitelů](./media/change-tracking-file-contents/changes.png)

Pomocí tohoto příkladu se podíváme na postup, jak na změnu vytvořit výstrahy.

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte možnost **sledování změn** a pak vyberte **Log Analytics**. 
2. V hledání v protokolech vyhledejte změny obsahu v souboru **hosts** pomocí dotazu `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Tento dotaz vyhledá změny obsahu souborů s plně kvalifikovanými názvy cest obsahujícími slovo `hosts` . Můžete také požádat o konkrétní soubor tak, že změníte část cesty na jeho plně kvalifikovaný tvar, například pomocí `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Poté, co dotaz vrátí své výsledky, klikněte na **nové pravidlo výstrahy** v hledání protokolu a otevřete stránku pro vytvoření výstrahy. Na tuto stránku můžete přejít také pomocí **Azure monitor** v Azure Portal. 

4. Znovu ověřte dotaz a upravte logiku výstrah. V takovém případě budete chtít aktivovat výstrahu, pokud je ve všech počítačích v prostředí zjištěna i jedna změna.

    ![Změnit na dotaz pro sledování změn v souboru Hosts](./media/change-tracking-file-contents/change-query.png)

5. Po nastavení logiky výstrah přiřaďte skupiny akcí, aby prováděly akce v reakci na aktivaci výstrahy. V tomto případě nastavili jsme e-maily, které se mají odeslat, a vytvoří se lístek ITSM (IT Service Management), který se má vytvořit. 

    ![Konfigurace skupiny akcí pro upozornění na změnu](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Další kroky

* Informace o konfiguracích oboru najdete v tématu [omezení Change Tracking a rozsahu nasazení inventáře](automation-scope-configurations-change-tracking.md).
* Pokud potřebujete hledat v protokolech uložených v pracovním prostoru Log Analytics, přečtěte si téma [prohledávání protokolů v](../azure-monitor/log-query/log-query-overview.md)protokolech Azure monitor.
* Pokud jste dokončili nasazení, přečtěte si téma [zrušení propojení pracovního prostoru s účtem Automation pro Change Tracking a inventář](automation-unlink-workspace-change-tracking.md).
* Pokud chcete virtuální počítače odstranit z Change Tracking a inventáře, přečtěte si téma [Odebrání virtuálních počítačů z Change Tracking a inventáře](automation-remove-vms-from-change-tracking.md).
* Řešení chyb funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).
