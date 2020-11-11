---
title: Nastavit vedoucímu projektu způsob, jakým Labs Azure Lab Services
description: Naučte se, jak nastavit testovací prostředí, aby projekt pomohly povést jako třídy.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496890"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Nastavení testovacích prostředí pro projekt vedoucí způsob, jakým třídy

[Vedoucí projektu (PLTW)](https://www.pltw.org/) je nezisková organizace, která poskytuje podrobné přehledy PreK-12 v rámci USA v počítačové vědy, inženýrské a biolékařské vědě.  V každé třídě PLTW studenti využívají různé softwarové aplikace jako součást praktických výukových zkušeností.  Mnohé ze softwarových aplikací vyžadují buď rychlý procesor, nebo v některých případech GPU.  V tomto článku se dozvíte, jak nastavit testovací prostředí pro následující třídy PLTW, které se obvykle nabízejí studentům ve stupních 9-12:

- **Seznámení se technickým návrhem**

    Studenti se zavádějí do procesu technického návrhu, který zahrnuje používání softwaru [CAD pro vytváření](https://www.autodesk.com/products/inventor/new-features) 3D modelů pomocí produktu Autodesk.

- **Principy strojírenství**
    
    Studenti se dozví o technických mechanismech, structural\material síle a automatizaci.  Tato třída používá software, jako jsou SSD, [Návrhář mostu](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)a [nepřerušovaná](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf) [simulace americkými](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Inženýrské strojírenství a architektura**

    Studenti jsou výukové sestavování a navrhování webů a jejich vývoje pomocí návrhového softwaru architektury [Revit společnosti Autodesk](https://www.autodesk.com/products/revit/overview) pro modelování 3D stavebních informací (BIM).

- **Výroba integrovaná do počítače**

    Studenti Prozkoumejte moderní výrobní procesy, které zahrnují automatické a automatizace.   V této třídě studenti využívají software společnosti [Autodesk pro inventarizaci CAD](https://www.autodesk.com/products/inventor/new-features) a [Autodesk (počítač s podporou výroby)](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Digitální elektronika**

    Studenti studují e-mailové okruhy a zařízení s využitím Multisim simulace a softwaru pro návrh okruhu [národního instrumentace](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) .

- **Návrh a vývoj pro strojírenství**

    Studenti přispívají do uceleného řešení, které kombinuje výzkum, návrh a testování, které jsou přítomny na panelu techniků.  V této třídě studenti využívají software [CAD pro inventarizaci společnosti Autodesk](https://www.autodesk.com/products/inventor/new-features) .

- **Základy počítačové vědy**

    Studenti se zavádějí do výpočetních konceptů a nástrojů.  Začínají s programováním na základě bloku a pak se přesunou na použití textového kódování pomocí prostředí kódování, jako jsou [bloky VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principy počítačové vědy**
    
    Studenti rozšiřují své zkušenosti s programováním v [Pythonu](https://www.python.org/) pomocí [Visual Studio Codeho vývojového prostředí Microsoftu](https://code.visualstudio.com/). 

- **Počítačové vědy A**

    Studenti rozšiřují své znalosti o programování v této třídě tím, že se naučí vývoj mobilních aplikací.  V této třídě se naučí [Java](https://www.java.com/) pomocí [Visual Studio Codeho vývojového prostředí Microsoftu](https://code.visualstudio.com/).  Studenti taky používají emulátor, který jim umožňuje spouštět a testovat kód mobilní aplikace.  Informace o tom, jak nastavit emulátor v Azure Labs, vám poskytneme na adrese azlabspilot@microsoft.com .

[Úplný seznam softwaru](https://www.pltw.org/pltw-software) pro každou třídu najdete v webu PLTW.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí
K nastavení Labs pro PLTW potřebujete předplatné Azure a účet testovacího prostředí, abyste mohli začít. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v kurzu [jak nastavit účet testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Můžete použít i existující účet testovacího prostředí.

Jakmile budete mít účet testovacího prostředí, měli byste vytvořit samostatné laboratoře pro každou relaci třídy PLTW, kterou nabízí vaše škola.  Doporučujeme také vytvořit samostatné image pro každý typ třídy PLTW.  Další informace o tom, jak strukturovat laboratoře a image, najdete v blogovém příspěvku: [Přechod z fyzického prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí
Pro účet testovacího prostředí povolte nastavení popsaná v následující tabulce. Další informace o tom, jak povolit image Marketplace, najdete v článku o [tom, jak zadat image z Marketplace dostupné pro tvůrce testovacích prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Nastavení účtu testovacího prostředí | Pokyny |
| -------------------- | ----- |
| Image z Marketplace | Povolte image Windows 10 pro pro použití v rámci vašeho účtu testovacího prostředí. |

### <a name="lab-settings"></a>Nastavení testovacího prostředí
Velikost virtuálního počítače, který doporučujeme použít pro třídy PLTW, závisí na typech úloh, které vaši studenti dělají ve třídě.  Pro výše uvedené třídy doporučujeme používat velké a malé grafické procesory (vizualizace) velikosti virtuálních počítačů.  Při nastavování testovacích prostředí pro třídy PLTW si přečtěte pokyny v následující tabulce.

| Nastavení testovacího prostředí | Hodnota/pokyny |
| ------------ | ------------------ |
|Velikost virtuálního počítače| **Malý grafický procesor (vizualizace)**.  Tento virtuální počítač je vhodný pro vzdálenou vizualizaci, streamování, hraní a kódování pomocí platforem, jako je OpenGL a DirectX.  Tuto velikost doporučujeme použít pro následující třídy PLTW: inženýrské strojírenství a architektura, Digital Electronics, výroba integrovaná do počítačů; a design a vývoj v technickém prostředí.
|Velikost virtuálního počítače| **Velký**.  Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti.  Tuto velikost doporučujeme použít pro následující třídy PLTW: Úvod k technickým návrhům, principům strojírenství, počítačových vědeckých Essentials, principům pro počítačové vědy a počítačové vědy a.

### <a name="licensing-server"></a>Licenční server
Většina softwaru používaného ve výše uvedených třídách PLTW do * *_Not_* _ vyžaduje přístup k licenčnímu serveru.  Budete ale muset získat přístup k licenčnímu serveru, pokud plánujete používat model licencování sítě společnosti Autodesk pro následující software:
-   Revit
-   Skladové
-   VAČKa zásob

Pokud chcete používat licencování sítě se softwarem společnosti Autodesk, [PLTW poskytuje podrobné kroky](https://www.pltw.org/pltw-software) pro instalaci správce licencí společnosti Autodesk na licenční server.  Tento licenční server se obvykle nachází v místní síti nebo je hostovaný na virtuálním počítači Azure v rámci služby Azure Virtual Network (VNet).

Po nastavení licenčního serveru budete muset [virtuální síť](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) navázat na svůj [účet testovacího prostředí](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account). Partnerský vztah k síti musí být dokončen _before * vytváření testovacího prostředí, aby virtuální počítače testovacího prostředí měly přístup k licenčnímu serveru a jiným způsobem.

Soubory s licencí generované společnosti Autodesk vloží adresu MAC licenčního serveru.  Pokud se rozhodnete hostovat licenční server pomocí virtuálního počítače Azure, je důležité se ujistit, že se adresa MAC licenčního serveru nemění.   V opačném případě se při změně adresy MAC bude nutné znovu vygenerovat soubory s licencí.  Pokud chcete zabránit změně adresy MAC, postupujte podle těchto tipů:

- [Nastavte statickou privátní IP adresu a adresu MAC](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address) pro virtuální počítač Azure, který je hostitelem vašeho licenčního serveru.
- Ujistěte se, že jste nastavili účet testovacího prostředí i virtuální síť licenčního serveru v region\location, která má dostatečnou kapacitu virtuálního počítače, takže nemusíte tyto prostředky přesunout na nové region\location později.

Další informace najdete také v článku o [Nastavení licenčního serveru jako sdíleného prostředku](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) .

### <a name="template-machine"></a>Počítač šablony
Některé instalační soubory, které potřebujete pro PLTW, jsou velké a při jejich stažení na počítač se šablonou testovacího prostředí se nemusejí trvat delší dobu.

Místo stahování instalačních souborů do počítače s šablonou a instalace všech těchto součástí doporučujeme vytvořit PLTW image ve vašem fyzickém prostředí.  Pak můžete bitové kopie importovat do galerie sdílených imagí, abyste je mohli použít k vytvoření cvičení.  Podrobnosti najdete v následujícím článku: [Nahrajte si vlastní image do galerie sdílených imagí](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery).

V rámci tohoto doporučení jsou zde uvedené hlavní úlohy pro nastavení testovacího prostředí:

1. Ve svém fyzickém prostředí vytvořte obrázek pro třídu.

    a.  Pomocí podrobného postupu PLTW Stáhněte instalační soubory a nainstalujte požadovaný software.

    > [!NOTE]    
    > Když nainstalujete aplikace Autodesk, počítač, na který instalujete Autodesk, musí být schopný komunikovat s vaším licenčním serverem (Průvodce instalací společnosti Autodesk vás vyzve k zadání názvu počítače, na kterém je licenční server hostovaný).  Pokud svůj licenční server Hostujte na VIRTUÁLNÍm počítači Azure, možná budete muset počkat na instalaci Autodesk na počítač šablony testovacího prostředí, aby mohl Průvodce instalací společnosti Autodesk získat přístup k licenčnímu serveru.

    b.  [Nainstalujte a nakonfigurujte OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (nebo jiné možnosti zálohování, které může vaše škola používat).
    
    c.  [Instalace a konfigurace aktualizací Windows](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates).

1.  Nahrajte vlastní image do [Galerie sdílených imagí, která je připojená k vašemu účtu testovacího prostředí](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery).

1.  Vytvořte testovací prostředí a vyberte vlastní image, kterou jste nahráli v předchozím kroku.

1.  Po vytvoření testovacího prostředí spusťte a připojte se k počítači šablony, abyste ověřili, že bitová kopie funguje podle očekávání.

1.  Nakonec publikujte počítač šablony a vytvořte virtuální počítače studentů.

## <a name="student-devices"></a>Zařízení studenta
Vaši studenti se můžou připojit ke svým testovacím virtuálním počítačům z počítačů s Windows\Mac a Chromebooks.  Tady jsou odkazy na pokyny pro každou z těchto možností:

- [Připojení z Windows](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Připojení z Mac](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Připojení z Chromebook](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>Cost
Pojďme pokrýt možné náklady na výše uvedené třídy PLTW.  Tento odhad nezahrnuje náklady na provozování licenčního serveru nebo použití Galerie sdílených imagí.  Použijeme třídu 25 studentů.  Naplánovaný čas třídy je 20 hodin.  Každý student také získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovaný čas třídy.  U **velkých** i **malých formátů GPU (vizualizace)** najdete níže odhadované náklady.

- **Velký virtuální počítač**

    25 studentů (20 naplánovaných hodin + 10 hodin) × 70 jednotek testovacího prostředí × 0,01 USD za hodinu = 525,00 USD

- **Malý grafický procesor (vizualizace)**

    25 studentů (20 naplánovaných hodin + 10 hodin) × 160 jednotek testovacího prostředí × 0,01 USD za hodinu = 1200,00 USD

> [!IMPORTANT] 
> Odhad nákladů slouží pouze jako příklad pro účely.  Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Mnohé z tříd PLTW využívají aplikace, ke kterým se dostanete prostřednictvím prohlížeče, jako je například inventarizace aplikací MIT.  Tyto aplikace založené na prohlížeči nevyžadují rychlý procesor nebo GPU a jsou dostupné z libovolného zařízení, které má připojení k Internetu.  Když studenti používají tyto typy aplikací, doporučujeme, aby používali prohlížeč na svém fyzickém zařízení namísto použití prohlížeče na svých testovacích virtuálních počítačích.  To vám pomůže snížit náklady, a to pouze pomocí testovacích počítačů v testovacím prostředí pro aplikace, které vyžadují rychlý procesor nebo GPU.

## <a name="next-steps"></a>Další kroky
Další kroky jsou běžné pro nastavení testovacího prostředí:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users). 