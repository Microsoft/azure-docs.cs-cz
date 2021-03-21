---
title: Správa Change Tracking a inventáře v Azure Automation
description: V tomto článku se dozvíte, jak pomocí Change Tracking a inventáře sledovat změny softwaru a služeb Microsoftu ve vašem prostředí.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: dff314f3c9fb72c565a7c2d522694d533c487895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572651"
---
# <a name="manage-change-tracking-and-inventory"></a>Správa řešení Change Tracking a Inventory

Když přidáte nový soubor nebo klíč registru, který se má sledovat, Azure Automation ho povolí pro [Change Tracking a inventář](overview.md). Tento článek popisuje, jak nakonfigurovat sledování, kontrolovat výsledky sledování a zpracovávat výstrahy při zjištění změn.

Než začnete používat postupy v tomto článku, ujistěte se, že jste povolili Change Tracking a inventarizaci na virtuálních počítačích pomocí jednoho z následujících postupů:

* [Povolení řešení Change Tracking a Inventory z účtu Automation](enable-from-automation-account.md)
* [Povolení Change Tracking a inventáře procházením Azure Portal](enable-from-portal.md)
* [Povolení řešení Change Tracking a Inventory z runbooku](enable-from-runbook.md)
* [Povolení řešení Change Tracking a Inventory z virtuálního počítače Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Omezení rozsahu nasazení

Change Tracking a inventář používá v rámci pracovního prostoru konfiguraci oboru pro cílení na počítače, aby přijímaly změny. Další informace najdete v tématu [omezení rozsahu nasazení Change Tracking a inventáře](manage-scope-configurations.md).

## <a name="track-files"></a>Sledování souborů

Pomocí Change Tracking a inventáře můžete sledovat změny souborů a složek nebo adresářů. V této části se dozvíte, jak nakonfigurovat sledování souborů ve Windows a v systému Linux.

### <a name="configure-file-tracking-on-windows"></a>Konfigurace sledování souborů ve Windows

Ke konfiguraci sledování souborů na počítačích se systémem Windows použijte následující postup:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Automation**. Po zahájení psaní seznam vyfiltruje návrhy na základě vašeho vstupu. Vyberte **Účty Automation**.

3. V seznamu účtů Automation vyberte účet, který jste zvolili při povolování Change Tracking a inventáře.

4. Ve svém účtu Automation v části **Správa konfigurace** vyberte **Change Tracking (sledování změn** ).

5. Vyberte **Upravit nastavení** (symbol ozubeného kolečka).

6. Na stránce Konfigurace pracovního prostoru vyberte **soubory Windows** a potom kliknutím na **+ Přidat** přidejte nový soubor, který chcete sledovat.

7. V podokně Přidat soubor Windows pro Change Tracking zadejte informace pro soubor nebo složku, které chcete sledovat, a klikněte na **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | True, pokud je nastavení použito, a jinak false.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Group (Skupina)     | Název skupiny pro logicky seskupené soubory.        |
    |Zadat cestu     | Cesta pro kontrolu souboru, například **c:\Temp \\ \* . txt**. Můžete také použít proměnné prostředí, například `%winDir%\System32\\\*.*` .       |
    |Typ cesty     | Typ cesty Možné hodnoty jsou soubor a složka.        |    
    |Rekurze     | True, pokud se používá rekurze při hledání položky, která se má sledovat, a v opačném případě false.        |    
    |Nahrát obsah souboru | True pro nahrání obsahu souboru při sledovaných změnách a v opačném případě false.|

    Pokud plánujete konfiguraci monitorování souborů a složek pomocí zástupných znaků, vezměte v úvahu následující skutečnosti:

    - Pro sledování více souborů jsou vyžadovány zástupné znaky.
    - Zástupné znaky se dají použít jenom v posledním segmentu cesty, jako je například *C:\folder\file* nebo */etc/*. conf *.
    - Pokud proměnná prostředí obsahuje cestu, která není platná, ověření proběhne úspěšně, ale při spuštění inventáře selže cesta.
    - Při nastavování cesty Vyhněte obecným cestám, jako je například *c:*. * *, což bude mít za následek příliš mnoho procházených složek.

8. Ujistěte se, že pro **nahrání obsahu souboru** zadáte hodnotu true. Toto nastavení povoluje sledování obsahu souborů pro určenou cestu k souboru.

### <a name="configure-file-tracking-on-linux"></a>Konfigurace sledování souborů v systému Linux

Ke konfiguraci sledování souborů na počítačích se systémem Linux použijte následující postup:

1. Vyberte **Upravit nastavení** (symbol ozubeného kolečka).

2. Na stránce Konfigurace pracovního prostoru vyberte možnost **soubory pro Linux** a potom vyberte **+ Přidat** a přidejte nový soubor, který chcete sledovat.

3. Na stránce **Přidat soubor pro Linux pro Change Tracking** zadejte informace o souboru nebo adresáři, který se má sledovat, a pak vyberte **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace.

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

4. Ujistěte se, že pro **nahrání obsahu souboru** zadáte **hodnotu true** . Toto nastavení povoluje sledování obsahu souborů pro určenou cestu k souboru.

   ![Přidat soubor pro Linux](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Sledovat obsah souboru

Sledování obsahu souborů umožňuje zobrazit obsah souboru před a po sledované změně. Tato funkce uloží obsah souboru do [účtu úložiště](../../storage/common/storage-account-overview.md) poté, co dojde ke změně. Tady jsou některá pravidla, která se mají dodržovat při sledování obsahu souboru:

* Pro ukládání obsahu souboru se vyžaduje účet úložiště úrovně Standard, který používá model nasazení Správce prostředků.
* Účty úložiště ve výchozím nastavení přijímají připojení z klientů v jakékoli síti. Pokud jste účet úložiště zabezpečili tak, aby povoloval jenom určitý provoz, musíte upravit svoje pravidla konfigurace, aby se k němu mohl připojit váš účet Automation. Viz [Konfigurace bran firewall a virtuálních sítí Azure Storage](../../storage/common/storage-network-security.md).
* Nepoužívejte účty úložiště modelu nasazení Premium a Classic. Přečtěte si informace [o Azure Storagech účtech](../../storage/common/storage-account-create.md).
* Účet úložiště můžete připojit jenom k jednomu účtu Automation.
* Change Tracking a inventář musí být ve vašem účtu Automation povolené.

### <a name="enable-tracking-for-file-content-changes"></a>Povolit sledování pro změny obsahu souborů

Chcete-li povolit sledování změn obsahu souboru, použijte následující postup:

1. Vyberte **Upravit nastavení** (symbol ozubeného kolečka).

2. Vyberte **obsah souboru** a pak vyberte **propojit**. Tento výběr otevře stránku **Přidat umístění obsahu pro Change Tracking** .

   ![Přidat umístění obsahu](./media/manage-change-tracking/enable.png)

3. Vyberte předplatné a účet úložiště, které chcete použít pro uložení obsahu souboru.

5. Pokud chcete povolit sledování obsahu souborů pro všechny existující sledované soubory, vyberte **zapnuto** pro **nahrání obsahu souboru pro všechna nastavení**. Toto nastavení můžete pro každou cestu souboru změnit později.

   ![Nastavit účet úložiště](./media/manage-change-tracking/storage-account.png)

6. Change Tracking a inventář zobrazuje identifikátory URI účtu úložiště a identifikátorů URI sdíleného přístupového podpisu (SAS), pokud umožňuje sledování změn obsahu souboru. Platnost podpisů vyprší po 365 dnech a můžete je znovu vytvořit výběrem možnosti **znovu vygenerovat**.

   ![Výpis klíčů účtu](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Zobrazení obsahu sledovaného souboru

Jakmile Change Tracking a inventář zjistí změnu pro sledovaný soubor, můžete zobrazit jeho obsah v podokně Podrobnosti o změně.  

![Zobrazit seznam změn](./media/manage-change-tracking/change-list.png)

1. Na stránce **Change Tracking (sledování změn** ) z účtu Automation zvolte soubor v seznamu změn a pak výběrem možnosti **Zobrazit změny obsahu souboru** zobrazte obsah souboru. V podokně Podrobnosti o změně se zobrazí standardní informace o souboru před a po a za každou vlastnost.

   ![Změnit podrobnosti](./media/manage-change-tracking/change-details.png)

2. Zobrazujete obsah souboru v zobrazení vedle sebe. Pokud chcete zobrazit vložené zobrazení změn, můžete vybrat možnost **inline** .

## <a name="track-registry-keys"></a>Sledovat klíče registru

Ke konfiguraci sledování klíčů registru v počítačích se systémem Windows použijte následující postup:

1. Na stránce **Change Tracking (sledování změn** ) účtu Automation vyberte **Upravit nastavení** (symbol ozubeného kolečka).

2. Na stránce Konfigurace pracovního prostoru vyberte možnost **registr systému Windows**.

3. Vyberte **+ Přidat** a přidejte nový klíč registru, který chcete sledovat.

4. Na stránce **Přidat registr systému Windows pro Change Tracking** zadejte informace pro klíč, který chcete sledovat, a pak vyberte **Uložit**. Následující tabulka definuje vlastnosti, které lze použít pro tyto informace. Když zadáte cestu k registru, musí se jednat o klíč a ne o hodnotu.

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

## <a name="next-steps"></a>Další kroky

* Informace o konfiguracích oboru najdete v tématu [omezení Change Tracking a rozsahu nasazení inventáře](manage-scope-configurations.md).
* Pokud potřebujete hledat protokoly uložené v protokolech Azure Monitor, přečtěte si téma [prohledávání protokolů v](../../azure-monitor/logs/log-query-overview.md)protokolech Azure monitor.
* Pokud jste dokončili nasazení, přečtěte si téma [odebrání Change Tracking a inventáře](remove-feature.md).
* Pokud chcete virtuální počítače odstranit z Change Tracking a inventáře, přečtěte si téma [Odebrání virtuálních počítačů z Change Tracking a inventáře](remove-vms-from-change-tracking.md).
* Řešení chyb funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](../troubleshoot/change-tracking.md).
