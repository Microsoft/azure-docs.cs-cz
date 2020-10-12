---
title: Nastavení testovacího prostředí pro výuku programu MATLAB pomocí Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí pro výuku programu MATLAB pomocí Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444994"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Nastavení testovacího prostředí pro výuku programu MATLAB

Program [MATLAB](https://www.mathworks.com/products/matlab.html), který představuje matricovou laboratoř, je programovací platforma z [MathWorks](https://www.mathworks.com/).  Kombinuje výpočetní výkon a vizualizaci díky oblíbeným nástrojům v oblastech matematických, inženýrských, fyzika a chemie.

Pokud používáte [licenci na úrovni areálu](https://www.mathworks.com/academia/tah-support-program/administrators.html), přečtěte si téma pokyny ke [Stažení instalačních souborů](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) nástroje MATLAB ke stažení instalačních souborů programu MATLAB na počítači šablony.  

V tomto článku si ukážeme, jak nastavit třídu, která používá klientský software MATLAB s licenčním serverem.

## <a name="license-server"></a>Licenční server

Před úpravou počítače šablony pro testovací prostředí budete muset nastavit server tak, aby spouštěl software [Správce síťových licencí](https://www.mathworks.com/help/install/administer-network-licenses.html) .  Tyto pokyny platí jenom pro instituce, které zvolí možnost licencování sítě pro MATLAB, která umožňuje uživatelům sdílet fond licenčních klíčů.  Budete také muset uložit licenční soubor a instalační klíč souboru pro pozdější instalaci.  Podrobné pokyny ke stažení licenčního souboru najdete v prvním kroku v článku [instalace správce síťové licence s připojením k Internetu](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) .

Podrobné pokyny týkající se instalace licenčního serveru jsou k dispozici v tématu [instalace správce síťové licence s připojením k Internetu](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Pokud chcete povolit výpůjčku, přečtěte si článek o [licenci k Dispůjčce](https://www.mathworks.com/help/install/license/borrow-licenses.html) .

Za předpokladu, že se licenční server nachází v místní síti nebo v privátní síti v rámci Azure, nezapomeňte [virtuální síť](how-to-connect-peer-virtual-network.md) navázat na svůj [účet testovacího prostředí](tutorial-setup-lab-account.md).  Partnerský vztah k síti je třeba provést před vytvořením testovacího prostředí, aby virtuální počítače testovacího prostředí mohly přistupovat k licenčnímu serveru.

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

K nastavení tohoto testovacího prostředí potřebujete předplatné Azure, abyste mohli začít.  Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/). Jakmile získáte předplatné Azure, můžete buď vytvořit nový účet testovacího prostředí v Azure Lab Services nebo použít existující účet.  Pokud chcete vytvořit nový účet testovacího prostředí, přečtěte si [kurz nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md).

Pokud chcete vytvořit nové testovací prostředí, postupujte podle [kurzu Vytvoření laboratorního prostředí pro učebnu](tutorial-setup-classroom-lab.md).  Použijte následující nastavení:

| Velikost virtuálního počítače | Image |
| -------------------- | ----- |
| Střední | Windows 10 |

MATLAB se podporuje na více operačních systémech.  Podrobnosti najdete v tématu [požadavky na systém pro Matlab](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) .

> [!WARNING]
> Před vytvořením testovacího prostředí nezapomeňte [virtuální síť](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) pro účet testovacího prostředí na virtuální síť pro daný licenční server.

## <a name="template-machine"></a>Počítač šablony

Po vytvoření počítače s šablonou spusťte počítač a připojte se k němu, abyste mohli dokončit následující hlavní úlohy.

1. Stáhněte instalační soubory pro klientský software MATLAB.
2. Nainstalujte MATLAB pomocí klíče pro instalaci souborů.

Instalace programu MATLAB bude proces více částí.  V první části se stáhnou soubory pro MATLAB a všechny další produkty, které chcete nainstalovat.  Použití klíče pro instalaci souborů vyžaduje, aby byly všechny instalační soubory pro produkty, které mají být nainstalovány, předem staženy.  Druhá část nainstaluje software MATLAB do šablony virtuálního počítače a aktivuje software.  Pokud je virtuální počítač šablony nakonfigurovaný k aktivaci pomocí licenčního serveru, budou virtuální počítače studenta stejné.

### <a name="download-installation-files"></a>Stažení instalačních souborů

Abyste mohli stáhnout instalační soubory a získat licenční soubor a instalační klíč souboru, musíte být správcem licence.  Postup stažení instalačních souborů najdete níže.

1. Přihlaste se ke svému účtu pro [https://www.mathworks.com](https://www.mathworks.com) .
2. Vyberte **můj účet**.
3. V části **můj software** na stránce účet klikněte na licenci připojenou k instalaci Správce síťových licencí pro testovací prostředí.
4. Na stránce s podrobnostmi o licenci klikněte na **Stáhnout produkty**.
5. Počkejte, než instalační program automaticky extrahuje.
6. Spusťte instalační program.  
7. Na stránce **Přihlásit se k účtu MathWorks** zadejte svůj účet MathWorks.
8. Na stránce **Licenční smlouva MathWorks** přijměte podmínky a klikněte na tlačítko **Další** .
9. Klikněte na rozevírací nabídku **Upřesnit možnosti** a vyberte možnost **Chci si stáhnout bez instalace**.
10. V nabídce **Vybrat cílovou složku**klikněte na **Další**.
11. Jako platformu počítače, do kterého budete instalovat MATLAB, vyberte **Windows** .
12. Na stránce **Vybrat produkt** se ujistěte, že je vybraná možnost MATLAB spolu s dalšími MathWorks produkty, které byste chtěli nainstalovat.
13. Na stránce **Potvrdit vybrané možnosti a stažení** klikněte na **zahájit stahování**.  
14. Počkejte, než se vybrané produkty stáhnou.  Klikněte na **Finish** (Dokončit).

Můžete si také stáhnout bitovou kopii ISO z webu MathWorks.

1. Přihlaste se ke svému účtu pro [https://www.mathworks.com](https://www.mathworks.com) .
2. Přejít na [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads) .
3. Vyberte verzi programu MATLAB, kterou chcete nainstalovat.
4. Klikněte na odkaz získat {Version}. ISO pod souvisejícími odkazy, kde {Version} je něco jako R2020a.
5. Klikněte na modrý odkaz na **verzi ke stažení** pro Windows.

### <a name="run-installer"></a>Spustit instalační program

Po stažení souborů je druhým krokem spuštění instalačního programu. Po opětovném dokončení tohoto kroku musíte být správcem licence.  MATLAB můžou nainstalovat jenom správci licencí pomocí klíče instalace souboru.

1. Zkontrolujte stažený soubor s licencí a ověřte, zda je na řádku serveru správně uveden seznam licencí.  Informace o tom, jak se má soubor s licencí formátovat, najdete v tématu [aktualizace síťové licence](https://www.mathworks.com/help/install/ug/network-license-files.html), [výpůjčky licencí](https://www.mathworks.com/help/install/license/borrow-licenses.html)a hledání článků s [ID hostitele](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) .
2. Spusťte instalační program pro MATLAB.
3. Na stránce **Přihlásit se k účtu MathWorks** zadejte svůj účet MathWorks.
4. Na stránce **Licenční smlouva MathWorks** přijměte podmínky a klikněte na tlačítko **Další** .
5. Klikněte na rozevírací nabídku **Upřesnit možnosti** a vyberte **mám instalační klíč souboru**.
6. Na stránce **instalovat pomocí instalačního klíče souboru** zadejte instalační klíč souboru pro licenční server.   Klikněte na **Next** (Další).
7. Na stránce **Vybrat soubor licence** přejděte na soubor s licencí uložený při stažení instalačních souborů dříve.
8. Na stránce **Vybrat cílovou složku** klikněte na **Další**.
9. Na stránce **vybrat produkty** klikněte na **Další**.
10. Na stránce **Vybrat možnosti** klikněte na **Další**.
11. Na stránce **Potvrdit vybrané možnosti a instalaci** klikněte na **zahájit instalaci**.
12. Na stránce **Instalace byla dokončena** ověřte, zda je zaškrtnuta možnost **aktivovat MATLAB** .  Klikněte na **Finish** (Dokončit).

## <a name="cost-estimate"></a>Odhad nákladů

Pojďme pro tuto třídu pokrýt možné náklady.  Tento odhad nezahrnuje náklady na provozování licenčního serveru.  Použijeme třídu 25 studentů.  Naplánovaný čas třídy je 20 hodin.  Každý student navíc získá kvótu 10 hodin pro domácí nebo přiřazení mimo plánovanou dobu třídy.  Velikost virtuálního počítače, kterou jsme zvolili, byla střední, což je 55 jednotek testovacího prostředí.

Zde je příklad možného odhadu nákladů pro tuto třídu:

25 studentů \* (20 naplánovaných hodin + 10 hodin kvóty) \* 55 jednotek testovacího prostředí \*  0,01 USD za hodinu = 412,50 USD

>[!IMPORTANT]
> Odhad nákladů slouží pouze jako příklad pro účely.  Aktuální podrobnosti o cenách najdete v tématu [Azure Lab Services ceny](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření, Správa a publikování šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)
