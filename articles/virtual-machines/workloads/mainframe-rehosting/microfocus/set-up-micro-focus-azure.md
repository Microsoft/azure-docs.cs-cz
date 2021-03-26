---
title: Nainstalujte Microal Enterprise Server 5,0 a Enterprise Developer 5,0 v Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak nainstalovat Micro Enterprise Server 5,0 a Enterprise Developer 5,0 na Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 5001f5df8ebab3e8366a2b31b1c5eb8ccc69c985
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951347"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Instalace Micro Micro Enterprise Server 5,0 a Enterprise Developer 5,0 v Azure

V tomto článku se dozvíte, jak nastavit [Micro Enterprise Server 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) a [Micro Enterprise Developer pro vývojáře 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) na Microsoft Azure.

Běžnou úlohou v Azure je vývojové a testovací prostředí. Tento scénář je běžný, protože je to tak nákladově efektivní a je snadné ho nasadit a vytrhnout. U podnikového serveru se na základě služby Micro Focus vytvořila jedna z největších dostupných platforem pro opětovné hostování sálových počítačů. Úlohy z/OS můžete spouštět na levnější platformě x86 v Azure pomocí virtuálních počítačů s Windows nebo Linux.

Tento instalační program používá virtuální počítače Azure s Windows serverem 2016 image z Azure Marketplace s už nainstalovanou Microsoft SQL Server 2017. Tato instalace platí také pro Azure Stack.

Odpovídající vývojové prostředí pro podnikový server je podnikovým vývojářem, který běží na Microsoft Visual Studio 2017 nebo novějším, v rámci sady Visual Studio Community (zdarma ke stažení) nebo na zatmění. Tento článek ukazuje, jak ho nasadit pomocí virtuálního počítače s Windows serverem 2016, který je součástí sady Visual Studio 2017 nebo novější.

## <a name="prerequisites"></a>Předpoklady

Než začnete, podívejte se na tyto požadavky:

-   Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

-   Software zaměřený na Micro a platnou licenci (nebo zkušební licenci). Pokud jste stávajícím zákazníkem s fokusem, obraťte se na svého zástupce Micro Focus. V opačném případě [požádejte o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

-   Získejte dokumentaci pro [podnikového serveru a podnikového vývojáře](https://www.microfocus.com/documentation/enterprise-developer/ed50/).

    > [!Note]
    > Pro řízení přístupu k virtuálním počítačům je k dispozici několik možností:
    > -   Osvědčeným postupem je nastavit [Azure bastionu](https://azure.microsoft.com/services/azure-bastion/).
    > -   Tunelové propojení typu [site-to-site sítě VPN](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) .
    > -   Virtuální počítač JumpBox.

## <a name="install-enterprise-server"></a>Instalace Enterprise Serveru

1.  Pro lepší zabezpečení a možnosti správy zvažte vytvoření nové skupiny prostředků jenom pro tento projekt, například **RGMicroFocusEntServer**. První část názvu v Azure použijte k výběru typu prostředku, který usnadňuje vymístění v seznamu.

2.  Vytvoří virtuální počítač. Z Azure Marketplace vyberte požadovaný virtuální počítač a požadovaný operační systém. Tady je doporučené nastavení:

    -   **Podnikový server:** Vyberte **virtuální počítač ES2 V3** (se 2 vCPU a 16 GB paměti) s nainstalovanými systémy Windows Server 2016 a SQL Server 2017. Tento obrázek je k dispozici z Azure Marketplace. Enterprise Server může používat i Azure SQL Database.

    -   **Podnikový vývojář:** Vyberte **virtuální počítač B2ms** (se 2 vCPU a 8 GB paměti) s nainstalovaným Windows 10 a sadou Visual Studio. Tento obrázek je k dispozici z Azure Marketplace.

3.  V části **základy** zadejte své uživatelské jméno a heslo. Vyberte **předplatné** a **umístění nebo oblast** , které chcete použít pro virtuální počítače. Jako skupinu prostředků vyberte **RGMicroFocusEntServer** .

4.  Oba virtuální počítače umístěte do stejné virtuální sítě, aby mohly vzájemně komunikovat.

5.  Pro zbývající nastavení přijměte výchozí hodnoty. Zapamatujte si uživatelské jméno a heslo, které vytvoříte pro správce těchto virtuálních počítačů.

6.  Po vytvoření virtuálních počítačů otevřete příchozí porty **9003, 86** a **80** pro HTTP a **3389** pro protokol RDP (Remote Desktop Protocol) (RDP) na počítači podnikového serveru a **3389** na počítači pro vývojáře.

7.  Pokud se chcete přihlásit k virtuálnímu počítači podnikového serveru, vyberte v Azure Portal virtuální počítač ES2 v3. V části **Přehled** vyberte **připojit** a spusťte relaci RDP. Přihlaste se pomocí přihlašovacích údajů, které jste vytvořili pro virtuální počítač.

8.  Z relace RDP načtěte následující dva soubory. Vzhledem k tomu, že používáte Windows, můžete soubory přetáhnout do relace RDP:

    -   `es\_50.exe`, instalační soubor podnikového serveru.

    -   `mflic`, odpovídající soubor s licencí – podnikový server se nebude načítat bez něj.

9.  Spusťte instalaci dvojím kliknutím na soubor. V prvním okně vyberte umístění instalace a přijměte licenční smlouvu s koncovým uživatelem.

    ![Snímek obrazovky se zobrazí dialogové okno Micro Enterprise Server, kde můžete spustit instalaci.](media/install-image-1.png)

    Po dokončení instalace se zobrazí následující zpráva:

    ![Snímek obrazovky se zobrazí zpráva o úspěchu v dialogovém okně Micro Enterprise Server – Enterprise.](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Vyhledat aktualizace

Po instalaci nezapomeňte zkontrolovat všechny další aktualizace, jako je třeba distribuovatelné součásti Microsoft C++ a .NET Framework, a to společně s podnikovým serverem.

### <a name="upload-the-license"></a>Odeslat licenci

1.  Začněte se správou licencí na Micro-Focus.

2.  Vyberte možnost **Spustit** \> **Nástroj** Správa licencí správce licencí na úrovni Micro \> a pak klikněte na kartu **instalovat** . Zvolte typ licenčního formátu, který se má nahrát: licenční soubor nebo kód s kódem 16 znaků. Například pro soubor v **souboru s licencí** přejděte do souboru *, který `mflic` jste předtím nahráli na virtuální počítač, a vyberte **instalovat licence**.

    ![Snímek obrazovky se zobrazí dialogové okno Správa licencí Micro box, kde můžete vybrat instalovat licence.](media/install-image-3.png)

3.  Ověřte, že se na podnikovém serveru načítá. Zkuste spustit web pro správu podnikového serveru z prohlížeče pomocí této adresy URL: `http://localhost:86/` . Zobrazí se stránka pro správu podnikového serveru, jak je znázorněno na obrázku.

    ![Stránka pro správu podnikového serveru](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instalace Enterprise Developer na počítač pro vývojáře

1.  Vyberte skupinu prostředků vytvořenou dříve (například **RGMicroFocusEntServer**) a pak vyberte vývojářskou image.

2.  Pokud se chcete přihlásit k virtuálnímu počítači, v části **Přehled** vyberte **připojit**. Toto přihlášení spustí relaci protokolu RDP. Přihlaste se pomocí přihlašovacích údajů, které jste vytvořili pro virtuální počítač.

3.  Z relace RDP načtěte následující dva soubory (Pokud chcete, můžete je přetáhnout):

    -   `edvs2017.exe`, instalační soubor podnikového serveru.

    -   `mflic`, odpovídající soubor s licencí (podniková vývojář nebude bez něj načten).

4.  Spusťte instalaci dvojím kliknutím na soubor **edvs2017.exe** . V prvním okně vyberte umístění instalace a přijměte licenční smlouvu s koncovým uživatelem. Pokud chcete, nainstalujte tento emulátor terminálu tak, že vyberete **instalovat Rumba 9,5** , který budete pravděpodobně potřebovat.

    ![Dialogové okno nastavení Micro Enterprise Developer pro Visual Studio 2017 – dialogové okno](media/install-image-5.png)

5.  Po dokončení instalace se zobrazí následující zpráva:

    ![Zpráva o úspěšné instalaci](media/install-image-6.png)

6.  Spusťte Správce licencí Micro Focus stejně jako u podnikového serveru. Vyberte možnost **Spustit** \> Nástroj Správa licencí správce licencí od **Micro Manageru** \> a klikněte na kartu **instalovat** .

7.  Vyberte typ licenčního formátu, který se má nahrát: soubor s licencí nebo kód licenčního kódu o 16 znacích. Například pro soubor v **souboru s licencí** přejděte do souboru, který `mflic` jste nahráli na virtuální počítač, a vyberte  **instalovat licence**.

    ![Dialogové okno Správa licencí Micro Focus](media/install-image-7.png)

Po načtení podnikového vývojáře se vaše nasazení vývojového a testovacího prostředí v Azure dokončí.

**Další kroky**

-   [Nastavení aplikace BankDemo](./demo.md)

-   [Provozování podnikového serveru v kontejnerech Docker](./run-enterprise-server-container.md)

-   [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)