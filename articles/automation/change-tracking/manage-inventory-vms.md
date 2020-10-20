---
title: Správa shromažďování Azure Automation inventáře z virtuálních počítačů | Microsoft Docs
description: V tomto článku se dozvíte, jak spravovat shromažďování inventáře z virtuálních počítačů.
services: automation
ms.subservice: change-inventory-management
keywords: inventory, automation, change, tracking
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2acf22ba76acdfa6152ef8966f4000aa325a9e91
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209636"
---
# <a name="manage-inventory-collection-from-vms"></a>Správa shromažďování dat pro inventarizaci z virtuálních počítačů

Sledování inventáře pro virtuální počítač Azure můžete povolit ze stránky prostředků v počítači. V počítačích můžete shromažďovat a zobrazovat tyto informace o inventáři:

- Aktualizace systému Windows, aplikace systému Windows, soubory a klíče registru
- Balíčky softwaru, procesy démony a soubory pro Linux

Azure Automation Change Tracking a inventář poskytují uživatelské rozhraní založené na prohlížeči pro nastavení a konfiguraci shromažďování inventáře.

## <a name="before-you-begin"></a>Než začnete

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

V tomto článku se předpokládá, že máte virtuální počítač, který se má povolit s Change Tracking a inventářem. Pokud nemáte virtuální počítač Azure, můžete [vytvořit virtuální počítač](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>Povolit shromažďování inventáře ze stránky prostředku virtuálního počítače

1. V levém podokně webu Azure Portal vyberte **Virtuální počítače**.
2. V seznamu virtuálních počítačů vyberte počítač.
3. V nabídce **prostředek** v části **operace**vyberte **inventarizace**.
4. Vyberte Log Analytics pracovní prostor pro ukládání datových protokolů.
    Pokud v dané oblasti nemáte k dispozici žádný pracovní prostor, zobrazí se výzva k vytvoření výchozího pracovního prostoru a účtu Automation.
5. Chcete-li začít počítač povolit, vyberte možnost **Povolit**.

   ![Zobrazení možností připojení](./media/manage-inventory-vms/inventory-onboarding-options.png)

    Stavový řádek vás upozorní, že je povolená funkce Change Tracking a inventáře. Tento proces může trvat až 15 minut. Během této doby můžete okno zavřít nebo ho můžete nechat otevřené a upozorní vás, když je funkce povolená. Stav nasazení můžete monitorovat v podokně oznámení.

   ![Zobrazení inventáře](./media/manage-inventory-vms/inventory-onboarded.png)

Po dokončení nasazení stavový řádek zmizí. Systém stále shromažďuje data inventáře, ale data ještě nemusí být viditelná. Úplné shromáždění dat může trvat až 24 hodin.

## <a name="configure-your-inventory-settings"></a>Konfigurace nastavení inventarizace

Ve výchozím nastavení jsou pro shromažďování nakonfigurovány software, služby Windows a linuxové procesy démon. Pokud chcete shromažďovat data pro inventarizaci registru a souborů systému Windows, nakonfigurujte nastavení shromažďování dat pro inventarizaci.

1. Na stránce inventarizace klikněte na **Upravit nastavení** v horní části stránky.
2. Chcete-li přidat nové nastavení kolekce, do kategorie nastavení, kterou chcete přidat, vyberte kartu soubory **registru systému Windows**, **soubory systému Windows**nebo **Linux** .
3. Vyberte příslušnou kategorii a klikněte na **Přidat** v horní části stránky.

Následující části obsahují informace o jednotlivých vlastnostech, které je možné nakonfigurovat pro různé kategorie.

### <a name="windows-registry"></a>Registr Windows

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Group (Skupina)     | Název skupiny pro logické seskupení souborů        |
|Klíč registru systému Windows   | Cesta, ve které se má soubor hledat. Například: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

### <a name="windows-files"></a>Soubory Windows

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | True, pokud je nastavení použito, a jinak false.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Group (Skupina)     | Název skupiny pro logicky seskupené soubory.       |
|Zadat cestu     | Cesta pro kontrolu souboru, například **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Soubory Linux

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | True, pokud je nastavení použito, a jinak false.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Group (Skupina)     | Název skupiny pro logicky seskupené soubory.        |
|Zadat cestu     | Cesta pro kontrolu souboru, například **/etc/*. conf**.       |
|Typ cesty     | Typ položky, která se má sledovat Hodnoty jsou soubor a adresář.        |
|Rekurze     | True, pokud se používá rekurze při hledání položky, která se má sledovat, a v opačném případě false.        |
|Použití sudo     | True, pokud se při kontrole položky používá sudo, a v opačném případě false.         |
|Odkazy     | Hodnota, která označuje, jak se zabývají symbolické odkazy při procházení adresářů. Možné hodnoty: <br> Ignorovat – Ignoruje symbolické odkazy a nezahrnuje odkazované soubory ani adresáře.<br>Sledovat – Během rekurze sleduje symbolické odkazy a zahrnuje i odkazované soubory a adresáře.<br>Spravovat – Sleduje symbolické odkazy a umožňuje zpracování vráceného obsahu.      |

## <a name="manage-machine-groups"></a>Správa skupin počítačů

Inventarizace umožňuje vytvářet a zobrazovat skupiny počítačů v protokolech Azure Monitor. Skupiny počítačů jsou kolekce počítačů definované dotazem v protokolech Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud chcete zobrazit skupiny počítačů, vyberte na stránce inventář kartu **skupiny počítačů** .

![Zobrazení skupin počítačů na stránce inventáře](./media/manage-inventory-vms/inventory-machine-groups.png)

Výběrem skupiny počítačů ze seznamu se otevře stránka skupiny počítačů. Tato stránka zobrazuje podrobnosti o skupině počítačů. Mezi tyto podrobnosti patří dotaz protokolu Azure Monitor, který se používá k definování skupiny. V dolní části stránky je seznam počítačů, které jsou součástí této skupiny.

![Zobrazit stránku skupiny počítačů](./media/manage-inventory-vms/machine-group-page.png)

Kliknutím na **+ klonovat** naklonujte skupinu počítačů. Skupině musíte přidělit nový název a alias pro skupinu. Definici lze v tomto okamžiku změnit. Po změně dotazu klikněte na **ověřit dotaz** a zobrazte náhled počítačů, které se mají vybrat. Pokud máte s touto skupinou spokojeni, kliknutím na **vytvořit** vytvořte skupinu počítačů.

Pokud chcete vytvořit novou skupinu počítačů, klikněte na **+ vytvořit skupinu počítačů**. Tímto tlačítkem otevřete stránku **vytvořit skupinu počítačů** , kde můžete definovat novou skupinu. Vytvořte skupinu kliknutím na **Vytvořit**.

![Vytvořit novou skupinu počítačů](./media/manage-inventory-vms/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>Odpojení virtuálního počítače od správy

Odebrání virtuálního počítače ze správy Change Tracking a inventáře:

1. V levém podokně Azure Portal vyberte **Log Analytics**a pak vyberte pracovní prostor, který jste použili při povolování virtuálního počítače pro Change Tracking a inventář.
2. Na stránce **Log Analytics** otevřete nabídku **prostředků** .
3. V části **zdroje dat pracovního prostoru**vyberte **Virtual Machines** .
4. V seznamu vyberte virtuální počítač, který chcete odpojit. Počítač má vedle **tohoto pracovního prostoru** ve sloupci **připojení OMS** zelenou značku zaškrtnutí.

   >[!NOTE]
   >Operations Management Suite (OMS) se teď označuje jako protokoly Azure Monitor.

5. V horní části Další stránky klikněte na **Odpojit**.
6. Kliknutím na tlačítko **Ano** v potvrzovacím okně odpojíte počítač se správou.

>[!NOTE]
>Počítače se pořád zobrazují i po jejich registraci, protože hlásíme na všech počítačích v inventáři za posledních 24 hodin. Po odpojení počítače musíte počkat 24 hodin, než se dostanou do seznamu.

## <a name="next-steps"></a>Další kroky

* Podrobnosti o práci s funkcí najdete v tématu [správa Change Tracking a inventáře](manage-change-tracking.md).
* Další informace o sledování změn softwaru najdete v tématu [sledování změn softwaru ve vašem prostředí pomocí Change Tracking](overview.md).
* Řešení obecných problémů s funkcí najdete v tématu [řešení potíží s Change Tracking a inventářem](../troubleshoot/change-tracking.md).
