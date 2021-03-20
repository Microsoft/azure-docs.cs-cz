---
title: Nastavit vedoucímu projektu způsob, jakým Labs Azure Lab Services
description: Naučte se, jak nastavit testovací prostředí, aby projekt pomohly povést jako třídy.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024615"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Nastavení testovacích prostředí pro projekt vedoucí způsob, jakým třídy

[Vedoucí projektu (PLTW)](https://www.pltw.org/) je nezisková organizace, která poskytuje PreK &ndash; 12 studijních programů napříč USA v počítačové vědy, inženýrské a biolékařské vědě.  V každé třídě PLTW studenti využívají různé softwarové aplikace jako součást praktických výukových zkušeností.  Mnohé ze softwarových aplikací vyžadují buď rychlý procesor, nebo, v některých případech GPU.  V tomto článku se dozvíte, jak nastavit testovací prostředí pro následující PLTW třídy, které jsou obvykle nabízeny studentům ve stupních 9 &ndash; 12:

- **Seznámení se technickým návrhem**

    Studenti se zavádějí do procesu inženýrské konstrukce, který zahrnuje použití softwaru CAD pro vytváření 3D modelování pomocí programu [Autodesk (Computer-Engineering design)](https://www.autodesk.com/products/inventor/new-features) .

- **Principy strojírenství**
    
    Studenti se dozví o technických mechanismech, strukturální a materiálové síle a automatizaci.  Tato třída používá software, jako jsou SSD, [Návrhář mostu](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)a [nepřerušovaná](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf) [simulace americkými](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Inženýrské strojírenství a architektura**

    Studenti se učí sestavování a navrhování webů a jejich vývoji pomocí softwaru pro návrh [Revit architektury Autodesk](https://www.autodesk.com/products/revit/overview) pro modelování BIM (3D Building Information Modeling).

- **Výroba integrovaná do počítače**

    Studenti Prozkoumejte moderní výrobní procesy, které zahrnují automatické a automatizace.   V této třídě studenti používají software [Autodesk inventarizace CAD](https://www.autodesk.com/products/inventor/new-features) a [Autodesk pro výrobu počítačového zpracování (vačk)](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Digitální elektronika**

    Studenti prostudují e-mailové okruhy a zařízení pomocí [národní aplikace Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) pro simulaci a navrhování okruhů.

- **Návrh a vývoj pro strojírenství**

    Studenti přispívají do uceleného řešení díky kombinaci výzkumu, návrhu a testování, které jsou přítomny na panelu techniků.  V této třídě studenti využívají software [Autodesk inventarizace Autodesk](https://www.autodesk.com/products/inventor/new-features) .

- **Základy počítačové vědy**

    Studenti se zavádějí do výpočetních konceptů a nástrojů.  Začínají s programováním na základě bloku a pak se přesunou na textové kódování pomocí prostředí kódování, jako jsou [bloky VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principy počítačové vědy**
    
    Studenti rozšiřují své zkušenosti s programováním v [Pythonu](https://www.python.org/) pomocí [prostředí pro vývoj kódu Microsoft Visual Studio](https://code.visualstudio.com/). 

- **Počítačové vědy A**

    Studenti rozšiřují své znalosti o programování v této třídě tím, že se naučí vývoj mobilních aplikací.  V této třídě se naučí [Java](https://www.java.com/) pomocí [prostředí pro vývoj kódu Microsoft Visual Studio](https://code.visualstudio.com/).  Studenti taky používají emulátor, který jim umožňuje spouštět a testovat kód mobilní aplikace.  Informace o tom, jak nastavit emulátor v Azure Lab Services, [získáte od Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com).

Úplný seznam softwaru třídy najdete na [webu PLTW](https://www.pltw.org/pltw-software) pro každou třídu.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

Pokud chcete začít nastavovat laboratoře pro PLTW, potřebujete předplatné Azure a účet testovacího prostředí. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). 

Po získání předplatného Azure můžete vytvořit nový účet testovacího prostředí v Azure Lab Services. Další informace o vytvoření nového účtu testovacího prostředí najdete v tématu [Nastavení účtu testovacího prostředí](./tutorial-setup-lab-account.md). Můžete použít i existující účet testovacího prostředí.

Po nastavení účtu testovacího prostředí byste měli vytvořit samostatné testovací prostředí pro každou relaci třídy PLTW, kterou vaše škola nabízí.  Doporučujeme také vytvořit samostatné image pro každý typ třídy PLTW.  Další informace o tom, jak strukturovat laboratoře a image, najdete v blogovém příspěvku, [který se přesouvá z fyzického prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Nastavení účtu testovacího prostředí

Povolte nastavení účtu testovacího prostředí, jak je popsáno v následující tabulce. Další informace o tom, jak povolit Azure Marketplace image, najdete v tématu [určení Azure Marketplace imagí dostupných pro tvůrce testovacích prostředí](./specify-marketplace-images.md).

| Nastavení účtu testovacího prostředí | Pokyny |
| -------------------- | ----- |
| Image z Marketplace | Povolte image Windows 10 pro pro použití v rámci vašeho účtu testovacího prostředí. |

<br>

### <a name="lab-settings"></a>Nastavení testovacího prostředí
Velikost virtuálního počítače (VM), který doporučujeme použít pro třídy PLTW, závisí na typech úloh, které vaši studenti dělají ve třídě.  Pro starší uvedené třídy doporučujeme použít malý grafický procesor (vizualizaci) a velké velikosti virtuálních počítačů. Při nastavování testovacích prostředí pro třídy PLTW se podívejte na pokyny v následující tabulce:

| Nastavení testovacího prostředí | Hodnota a popis | Doporučení třídy |
| ------------ | ------------------ | --- |
| Velikost virtuálního počítače | **Malý grafický procesor (vizualizace)**<br>Nejvhodnější pro vzdálenou vizualizaci, streamování, hraní a kódování pomocí platforem, jako je OpenGL a DirectX. | Tuto velikost doporučujeme použít pro následující třídy PLTW: inženýrské strojírenství a architekturu, Digital Electronics, výroba integrovaná do počítačů a návrh a vývoj v technikách.
| Velikost virtuálního počítače | **Velká**<br>Nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti. | Tuto velikost doporučujeme použít pro následující třídy PLTW: Úvod k technickým návrhům, principům strojírenství, počítačových vědeckých Essentials, principům pro počítačové vědy a počítačové vědy a. |

<br>

### <a name="license-server"></a>Licenční server
Většina softwaru, který se používá ve výše zmíněných třídách PLTW, *nepotřebuje přístup* k licenčnímu serveru.  Pokud ale plánujete použít model licencování sítě Autodesk pro následující software, budete potřebovat přístup k licenčnímu serveru:
-   Revit
-   Skladové
-   VAČKa zásob

Pokud chcete používat licencování sítě se softwarem Autodesk, [PLTW poskytuje podrobné pokyny](https://www.pltw.org/pltw-software) k instalaci Správce síťových licencí společnosti Autodesk na váš licenční server.  Tento licenční server se obvykle nachází v místní síti nebo je hostovaný na virtuálním počítači Azure v rámci služby Azure Virtual Network.

Po nastavení licenčního serveru budete muset vytvořit partnerský vztah k [virtuální síti](./how-to-connect-peer-virtual-network.md) s vaším [účtem testovacího prostředí](./tutorial-setup-lab-account.md). *Před* vytvořením testovacího prostředí je potřeba vytvořit partnerský vztah k síti, aby vaše virtuální počítače v testovacím prostředí měly přístup k licenčnímu serveru a naopak.

Soubory s licencí generované společností Autodesk vloží adresu MAC licenčního serveru.  Pokud se rozhodnete hostovat váš licenční server pomocí virtuálního počítače Azure, je důležité se ujistit, že se adresa MAC licenčního serveru nemění. Pokud se adresa MAC změní, budete muset znovu vygenerovat své licenční soubory. Pokud chcete zabránit tomu, aby se vaše adresa MAC mohla měnit, udělejte toto:

- [Nastavte statickou privátní IP adresu a adresu MAC](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) pro virtuální počítač Azure, který je hostitelem vašeho licenčního serveru.
- Nezapomeňte nastavit účet testovacího prostředí i virtuální síť licenčního serveru v oblasti nebo umístění s dostatečnou kapacitou virtuálního počítače, abyste tyto prostředky nemuseli přesunout do nové oblasti nebo umístění později.

Další informace najdete v tématu [Nastavení licenčního serveru jako sdíleného prostředku](./how-to-create-a-lab-with-shared-resource.md).

### <a name="template-machine"></a>Počítač šablony
Některé instalační soubory, které potřebujete pro PLTW, jsou velké. Když stahujete soubory do testovacího virtuálního počítače šablony testovacího prostředí, může kopírování trvat dlouhou dobu.

Místo stahování instalačních souborů do počítače s šablonou a instalace všeho tam doporučujeme vytvořit image PLTW ve svém fyzickém prostředí.  Pak můžete vlastní image importovat do galerie sdílených imagí, abyste je mohli použít k vytvoření cvičení.  Další informace najdete v tématu [nahrání vlastní image do galerie sdílených imagí](./upload-custom-image-shared-image-gallery.md).

Jak budete postupovat podle tohoto doporučení, poznamenejte si hlavní úlohy pro nastavení testovacího prostředí:

1. Ve svém fyzickém prostředí vytvořte obrázek pro třídu.

    a.  Pomocí podrobného postupu PLTW Stáhněte instalační soubory a nainstalujte požadovaný software.

    > [!NOTE]    
    > Když instalujete aplikace Autodesk, počítač, na který je instalujete, musí být schopný komunikovat s vaším licenčním serverem. Průvodce instalací aplikace Autodesk vás vyzve k zadání názvu počítače, na kterém je licenční server hostovaný.  Pokud máte svůj licenční server hostovaný na virtuálním počítači Azure, možná budete muset počkat na instalaci Autodesk na virtuálním počítači šablony testovacího prostředí, aby mohl Průvodce instalací získat přístup k vašemu licenčnímu serveru.

    b.  [Nainstalujte a nakonfigurujte OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) nebo jiné možnosti zálohování, které může vaše škola používat.
    
    c.  [Instalace a konfigurace aktualizací Windows](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Nahrajte vlastní image do [Galerie sdílených imagí, která je připojená k vašemu účtu testovacího prostředí](./how-to-attach-detach-shared-image-gallery.md).

1.  Vytvořte testovací prostředí a pak vyberte vlastní image, kterou jste nahráli v předchozím kroku.

1.  Po vytvoření testovacího prostředí spusťte a připojte se k virtuálnímu počítači šablony, abyste ověřili, že bitová kopie funguje podle očekávání.

1.  Nakonec publikujte virtuální počítač šablony a vytvořte virtuální počítače studentů.

## <a name="student-devices"></a>Zařízení studenta
Studenti se můžou připojit ke svým testovacím virtuálním počítačům z počítačů s Windows, Mac a Chromebook. Pokyny najdete tady:

- [Připojení z Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Připojení z Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Připojení z Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Náklady
Pojďme pokrýt příklad odhadu nákladů pro třídy PLTW.  Tento odhad nezahrnuje náklady na spuštění licenčního serveru nebo použití Galerie sdílených imagí. Předpokládejme, že máte třídu 25 studentů, z nichž každý má 20 hodin plánovaného času třídy.  Každý student má také dalších 10 hodinových hodin pro domácí úkoly nebo přiřazení mimo plánovaný čas třídy.  Zde jsou odhadované náklady:

- **Velký virtuální počítač**

    25 studentů &times; (20 naplánovaných hodin + 10 hodin za kvótu) &times; 70 jednotek testovacího prostředí &times; : 1 – 0,01 za hodinu = 525.00 USD

- **Malý grafický procesor (vizualizace)**

    25 studentů &times; (20 naplánovaných hodin + 10 hodin za kvótu) &times; 160 jednotek testovacího prostředí &times; : 1 – 0,01 za hodinu = 1200.00 USD

> [!IMPORTANT] 
> Odhad nákladů slouží pouze jako příklad pro účely.  Aktuální informace o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Mnohé z tříd PLTW využívají aplikace, ke kterým se dostanete prostřednictvím prohlížeče, jako je například inventarizace aplikací MIT.  Tyto aplikace založené na prohlížeči nevyžadují rychlý procesor ani GPU a k nim můžete přistupovat z libovolného zařízení, které má připojení k Internetu.  Když studenti používají tyto typy aplikací, doporučujeme, aby používali prohlížeč na svém fyzickém zařízení místo prohlížeče na svém testovacím virtuálním počítači. Studenti můžou snížit náklady rychleji pomocí testovacího virtuálního počítače jenom pro aplikace, které vyžadují rychlý procesor nebo GPU.

## <a name="next-steps"></a>Další kroky

Při nastavování testovacího prostředí si přečtěte následující články:

- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavení kvót](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users) 
