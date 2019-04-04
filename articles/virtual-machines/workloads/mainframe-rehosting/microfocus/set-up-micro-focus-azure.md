---
title: Instalace Micro fokus Enterprise Server 4.0 a podnikový vývojář 4.0 v Azure | Dokumentace Microsoftu
description: Změna hostitele vaše úlohy sálové počítače IBM z/OS pomocí Micro zaměření vývojových a testovacích prostředích na Azure virtual machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 33d0baf10df1882baf212c3e2c2683c8ca072fcc
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896326"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalace Micro fokus Enterprise Server 4.0 a podnikový vývojář 4.0 v Azure

Tento článek popisuje, jak nastavit [Micro fokus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) a [Micro fokus Enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) v Azure.

Běžné úlohy v Azure je vývojová a testovací prostředí, protože je to nákladově efektivní a snadno nasazovat a odstraňovat. Se serverem Enterprise Micro fokus vytvořil jeden z největších mainframových rehosting platformy k dispozici. Z/OS můžete spouštět na levnější x86 platform v Azure pomocí služby Windows nebo Linux virtual machines (VM).

Toto nastavení používá virtuální počítače Azure spuštěná image Windows serveru 2016 z webu Azure Marketplace s Microsoft SQL Server 2017 už nainstalovaná. Toto nastavení platí také pro Azure Stack.

Odpovídající vývojové prostředí pro Enterprise Server je podnikový vývojář, který se spouští na buď Microsoft Visual Studio 2017, Visual Studio Community (zdarma ke stažení), nebo Eclipse. Tento článek popisuje, jak se dá nasadit pomocí virtuálního počítače s Windows serverem 2016, která se dodává s nainstalovanou sadu Visual Studio 2017.

## <a name="prerequisites"></a>Požadavky

Než začnete, projděte si tyto požadavky:

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Software Micro fokus a platnou licenci (nebo licenci na zkušební verzi). Pokud jste stávající zákazník Micro fokus, obraťte se na zástupce Micro fokus. V opačném případě [požádat o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Získejte dokumentaci pro [Enterprise Server a podnikový vývojář](https://www.microfocus.com/documentation/enterprise-developer/#").

> [!NOTE]
> Osvědčeným postupem je zřídit tunelové připojení virtuální privátní sítě site-to-site (VPN) nebo jumpbox tak můžete řídit přístup k virtuálním počítačům Azure.

## <a name="install-enterprise-server"></a>Instalace serveru pro Enterprise

1. Pro lepší zabezpečení a možnosti správy, zvažte možnost vytvořit novou skupinu prostředků jen pro tento projekt, například **RGMicroFocusEntServer**. První část názvu v Azure použijte k určení typ prostředku, aby bylo snazší pro přímé v seznamu.

2. Vytvoří virtuální počítač. Z webu Azure Marketplace vyberte virtuální počítač a operačního systému, který chcete. Tady se o doporučené nastavení:

    - **Enterprise Server**: Vyberte ES2 v3 virtuální počítač (pomocí 2 virtuální procesory a 16 GB paměti) s Windows serverem 2016 a nainstalovaným SQL serverem 2017. Tento obrázek pochází z Azure Marketplace k dispozici. Enterprise Server můžete použít také Azure SQL Database.

    - **Podnikový vývojář**: Vyberte virtuální počítač B2ms (s 2 virtuálních procesorů a 8 GB paměti) s Windows 10 a nainstalovanou sadu Visual Studio. Tento obrázek pochází z Azure Marketplace k dispozici.

3. Na **Základy** okno, zadejte svoje uživatelské jméno a heslo. Vyberte **předplatné** a **umístění nebo oblasti** chcete použít pro virtuální počítače. Vyberte **RGMicroFocusEntServer** pro skupinu prostředků.

4. Oba virtuální počítače umístěte do stejné virtuální síti, tak může komunikovat mezi sebou.

5. Přijměte výchozí hodnoty pro zbývající nastavení. Mějte na paměti, uživatelské jméno a heslo, které vytvoříte pro správce těchto virtuálních počítačů.

6. Po vytvoření virtuálních počítačů otevřít příchozí porty 9003, 86 a 80 pro protokol HTTP a port 3389 pro RDP na počítači serveru organizace a 3389 na počítači pro vývojáře.

7. Pro přihlášení k virtuálnímu počítači Enterprise serveru, webu Azure Portal, vyberte virtuální počítač ES2 v3. Přejděte **přehled** okna a vyberte **připojit** spustit relaci RDP. Přihlaste se pomocí přihlašovacích údajů, které jste vytvořili pro virtuální počítač.

8. Z relace protokolu RDP načtěte následující dva soubory. Protože se jedná o Windows, takže je můžete přetáhnout soubory do relace protokolu RDP:

    - **ES\_40. exe**, Enterprise Server instalační soubor.

    - **mflic**, odpovídající soubor licence – Enterprise Server nenačte bez něj.

9. Poklikejte na soubor a spusťte instalaci. V prvním okně zvolte umístění instalace a přijetí licenční smlouvy s koncovým uživatelem.

     ![Na obrazovce Micro fokus Enterprise serveru instalace](media/01-enterprise-server.png)

     Když se instalace dokončí, zobrazí se následující zpráva:

     ![Na obrazovce Micro fokus Enterprise serveru instalace](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Vyhledat aktualizace

Po instalaci je potřeba zkontrolovat pro všechny další aktualizace od počet požadavků, jako například distribuovatelné součásti Microsoft C++ a .NET Framework jsou nainstalovány společně s Enterprise Server.

### <a name="upload-the-license"></a>Nahrát licence

1. Spusťte správu licencí Micro fokus.

2. Klikněte na tlačítko **Start** \> **správce licencí fokus Micro** \> **správy licencí**a potom klikněte na tlačítko **nainstalovat** kartu. Zvolte typ formátu licence k nahrání: licenční soubor nebo 16 znacích licenční kód. Třeba u souborů v **licenční soubor**, přejděte **mflic** dříve nahraje soubor do virtuálního počítače a vyberte **nainstalovat licence**.

     ![Dialogové okno Micro správu licencí fokus](media/03-enterprise-server.png)

3. Ověřte, že načte Enterprise Server. Zkuste webu Enterprise serveru správy z prohlížeče pomocí této adresy URL <http://localhost:86/> . Jak je znázorněno, zobrazí se stránka správy Enterprise serverů.

     ![Stránka Správa Server Enterprise](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Nainstalujte na počítači pro vývojáře pro podnikové vývojáře

1. Vyberte dříve vytvořenou skupinu prostředků (například **RGMicroFocusEntServer**), pak vyberte bitovou kopii pro vývojáře.

2. Přihlásit se k virtuálnímu počítači, přejděte **přehled** okna a vyberte **připojit**. Tím se spustí relaci RDP. Přihlaste se pomocí přihlašovacích údajů, které jste vytvořili pro virtuální počítač.

3. Z relace protokolu RDP načtěte následující dva soubory (přetažení Pokud se vám líbí):

    - **edvs2017.exe**, Enterprise Server instalační soubor.

    - **mflic**, odpovídající licenční soubor (pro podnikové vývojáře nenačte bez něj).

4. Dvakrát klikněte **edvs2017.exe** souboru spusťte instalaci. V prvním okně zvolte umístění instalace a přijetí licenční smlouvy s koncovým uživatelem. Pokud chcete, zvolte **instalace programu Rumba 9.5** nainstalovat tento emulaci terminálu, které budete pravděpodobně potřebovat.

     ![Podnikový vývojář Micro fokus pro dialogové okno instalace aplikace Visual Studio 2017](media/04-enterprise-server.png)

5. Po dokončení instalace se zobrazí následující zpráva:

     ![Instalační program úspěšně zprávy](media/05-enterprise-server.png)

6. Stejně jako jste to udělali pro Enterprise serveru, spusťte Správce Micro fokus licence. Zvolte **Start** \> **správce licencí fokus Micro** \> **správu licencí**a klikněte na tlačítko **nainstalovat**kartu.

7. Zvolte typ formátu licence k nahrání: licenční soubor nebo 16 znacích licenční kód. Třeba u souborů v **licenční soubor**, přejděte **mflic** dříve nahraje soubor do virtuálního počítače a vyberte **nainstalovat licence**.

     ![Dialogové okno Micro správu licencí fokus](/edia/07-enterprise-server.png)

Při načtení pro podnikové vývojáře, vaše nasazení Micro zaměření vývojových a testovacích prostředí v Azure je hotová.

## <a name="next-steps"></a>Další postup

- [Nastavit Bank ukázkové aplikace](./demo.md)
- [Spuštění v kontejnerech Docker Enterprise Server](./run-enterprise-server-container.md)
- [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
