---
title: Nastavení testovacího prostředí pro ArcMap\ArcGIS Desktop s Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro třídy pomocí ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: dbe4191b64773b71bc1ae04842d824fbfead8e55
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628154"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Nastavení testovacího prostředí pro ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) je typ geografického informačního systému (GIS).  ArcGIS se používá k make\analyzeí map a práci s geografickými daty, která poskytuje služba [Research Institute of ESRI (environmentální systémy Research Institute](https://www.esri.com/en-us/home) ).  I když ArcGIS Desktop obsahuje několik aplikací, Tento článek popisuje, jak nastavit laboratoře pro použití ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) se používá k vytváření, úpravám a analýze 2D map.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K zahájení nastavení testovacího prostředí pro používání ArcMap potřebujete předplatné Azure a účet testovacího prostředí.  Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services.  Další informace o vytvoření nového účtu testovacího prostředí najdete v tématu [Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).  Můžete použít i existující účet testovacího prostředí.

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Povolte nastavení účtu testovacího prostředí, jak je popsáno v následující tabulce.  Další informace o tom, jak povolit Azure Marketplace image, najdete v tématu [určení Azure Marketplace imagí dostupných pro tvůrce testovacích prostředí](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images).

| Nastavení účtu testovacího prostředí | Pokyny |
| ------------------- | ------------ |
|Image z Marketplace| Povolte image Windows 10 pro nebo Windows 10 pro N pro použití v rámci vašeho účtu testovacího prostředí.|

### <a name="licensing-server"></a>Licenční server

Jeden typ licencování, který ArcGIS Desktop nabízí, je [Souběžné použití licencí](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  To vyžaduje, abyste na licenční server nainstalovali ArcGIS License Manager.  Správce licencí sleduje počet kopií softwaru, které lze spustit současně.  Další informace o tom, jak nastavit správce licencí na serveru, najdete v [Průvodci pro správce licencí](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

Licenční server se obvykle nachází v místní síti nebo je hostovaný na virtuálním počítači Azure v rámci virtuální sítě Azure.  Po nastavení licenčního serveru budete muset vytvořit partnerský vztah k [virtuální síti](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network) s vaším [účtem testovacího prostředí](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account).  Před vytvořením testovacího prostředí je potřeba vytvořit partnerský vztah k síti, aby vaše virtuální počítače v testovacím prostředí měly přístup k licenčnímu serveru a naopak.

Další informace najdete v tématu [Nastavení licenčního serveru jako sdíleného prostředku](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Nastavení testovacího prostředí

Velikost virtuálního počítače (VM), který doporučujeme použít pro ArcGIS Desktop, závisí na aplikacích, rozšířeních a konkrétních verzích, které studenti použijí.  Velikost virtuálního počítače také závisí na úlohách, které mají studenti očekávat.  Informace o tom, jak určit velikost virtuálního počítače, najdete v tématu [požadavky na systém ArcGIS Desktop](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) .  Po identifikaci potenciální velikosti virtuálního počítače doporučujeme, abyste otestovali úlohy studentů, abyste zajistili odpovídající výkon.

V tomto článku doporučujeme používat pro [10.7.1 verze ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm) [  velikost virtuálního počítače](administrator-guide.md#vm-sizing) za předpokladu, že se nepoužijí žádná jiná rozšíření ArcGIS Desktop.  V závislosti na potřebách vaší třídy ale můžete vyžadovat **velkou** nebo dokonce velikost virtuálního počítače **Small\Medium GPU (vizualizace)** .  Například [rozšíření prostorového analytika](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) , které je součástí ArcGIS desktopu, podporuje GPU pro lepší výkon, ale nevyžaduje použití GPU.

| Nastavení testovacího prostředí | Hodnota a popis |
| ------------ | ------------------ |
|Velikost virtuálního počítače| **Střední**.  Nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy.|  

### <a name="template-machine"></a>Počítač šablony

Postup v této části ukazuje, jak nastavit virtuální počítač šablony:

1.  Spusťte šablonu virtuálního počítače a připojte se k počítači pomocí protokolu RDP.

2.  Stáhněte a nainstalujte desktopové komponenty ArcGIS pomocí instrukcí od společnosti ESRI.  K těmto krokům patří přiřazení správce licencí pro souběžné používání licencí: 
    - [Úvod k instalaci a konfiguraci ArcGIS desktopu](https://desktop.arcgis.com/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Nastavte externí úložiště záloh pro studenty.  Studenti můžou ukládat soubory přímo na jejich přiřazený virtuální počítač, protože všechny změny, které provedou, se ukládají napříč relacemi.  Nicméně doporučujeme, aby studenti zálohovali svou práci do úložiště, které je externí z jejich virtuálního počítače, z několika důvodů:
    - Pokud chcete studentům umožnit přístup k práci po ukončení třídy a testovacího prostředí.  
    - V případě, že Student získá svůj virtuální počítač do chybného stavu a jejich image se musí [resetovat](how-to-set-virtual-machine-passwords.md#reset-vms).

    V ArcGIS by měl každý student na konci každé pracovní relace zálohovat následující soubory:

    - soubor MXD, který ukládá informace o rozložení projektu.
    - Souborové databáze, které ukládají všechna data vytvořená pomocí ArcGIS.
    - Všechna ostatní data, která může student používat jako rastrové soubory, shapefile, intiff atd.

    Pro úložiště zálohování doporučujeme použít OneDrive.  Pokud chcete nastavit OneDrive na virtuálním počítači šablony, postupujte podle kroků uvedených v článku [instalace a konfigurace OneDrivu](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Nakonec [publikujte](how-to-create-manage-template.md#publish-the-template-vm) virtuální počítač šablony a vytvořte virtuální počítač studentů.

### <a name="auto-shutdown-and-disconnect-settings"></a>Nastavení automatického vypnutí a odpojení

[Nastavení automatického vypnutí a odpojení](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) testovacího prostředí vám pomůžou zajistit, aby se virtuální počítač studenta vypnul, když se nepoužívá.  Tato nastavení by měla být nastavená na základě typů úloh, které budou vaši studenti provádět, aby se jejich virtuální počítač neukončil uprostřed jejich práce.  Například možnost **Odpojit uživatele, když jsou virtuální počítače v nečinnosti** , odpojí studenta od své relace RDP po zjištění, že se pro zadanou dobu nezjistily žádné vstupy myší nebo klávesnicí.  Toto nastavení musí umožňovat dostatek času pro úlohy, ve kterých student nepoužívá myš ani klávesnici, například pro spouštění dlouhých dotazů nebo čekání na vykreslení.

Pro ArcGIS doporučujeme pro tato nastavení použít následující hodnoty:
- Odpojit uživatele, když jsou virtuální počítače nečinné
    - 30 minut po zjištění stavu nečinnosti
- Vypnutí virtuálních počítačů při odpojení uživatele
    - 15 minut po odpojení uživatele

## <a name="cost"></a>Náklady

Pojďme pro tuto třídu pokrýt možné náklady. Tento odhad nezahrnuje náklady na provozování licenčního serveru. Použijeme třídu 25 studentů. Naplánovaný čas třídy je 20 hodin. Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy. Velikost virtuálního počítače, kterou jsme zvolili, byla **střední**, což je 42 jednotek testovacího prostředí.

25 studentů \* (20 naplánovaných hodin + 10 hodin kvóty) \* 42 jednotek testovacího prostředí × 0,01 USD za hodinu = 315,00 USD

>[!IMPORTANT]
> Odhad nákladů slouží pouze jako příklad pro účely.  Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)