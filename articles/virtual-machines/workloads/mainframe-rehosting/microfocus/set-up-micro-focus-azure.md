---
title: Instalace micro Focus Enterprise Server 4.0 a Enterprise Developer 4.0 do Azure | Dokumenty společnosti Microsoft
description: Znovu hostujte úlohy sálových počítačů IBM z/OS pomocí vývojového a testovacího prostředí Micro Focus na virtuálních počítačích (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411195"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Instalace Micro Focus Enterprise Server 4.0 a Enterprise Developer 4.0 do Azure

Tento článek ukazuje, jak nastavit [Micro Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) a Micro Focus Enterprise Developer [4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) v Azure.

Běžné zatížení v Azure je vývojové a testovací prostředí. Tento scénář je běžné, protože je to tak nákladově efektivní a snadno nasadit a strhnout. S Enterprise Server, Micro Focus vytvořil jeden z největších mainframe rehosting platformy k dispozici. Úlohy z/OS můžete spouštět na levnější platformě x86 v Azure pomocí virtuálních počítačů s Windows nebo Linuxem.

> [!NOTE]
> Již brzy: Pokyny pro nastavení [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) na virtuálních počítačích Azure.

Toto nastavení používá virtuální počítače Azure s bitovou kopií Windows Serveru 2016 z Azure Marketplace s už nainstalovaným Microsoft SQL Serverem 2017. Toto nastavení platí také pro Azure Stack.

Odpovídající vývojové prostředí pro Enterprise Server je Enterprise Developer, který běží na Microsoft Visual Studio 2017 nebo novější, Visual Studio Community (zdarma ke stažení) nebo Eclipse. Tento článek ukazuje, jak ji nasadit pomocí virtuálního počítače se systémem Windows Server 2016, který je dodáván s nainstalovaným Visual Studio 2017 nebo novějším.

## <a name="prerequisites"></a>Požadavky

Než začnete, podívejte se na tyto požadavky:

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Software Micro Focus a platná licence (nebo zkušební licence). Pokud jste stávajícím zákazníkem společnosti Micro Focus, obraťte se na svého zástupce společnosti Micro Focus. V opačném případě [požádejte o zkušební verzi](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- Získejte dokumentaci pro [podnikové servery a vývojáře .](https://www.microfocus.com/documentation/enterprise-developer/#")

> [!NOTE]
> Osvědčeným postupem je nastavení tunelového propojení virtuální privátní sítě (VPN) mezi lokalitami nebo jumpboxu, abyste mohli řídit přístup k virtuálním počítačům Azure.

## <a name="install-enterprise-server"></a>Instalace Enterprise Serveru

1. Pro lepší zabezpečení a možnosti správy zvažte vytvoření nové skupiny prostředků pouze pro tento projekt – například **RGMicroFocusEntServer**. Pomocí první části názvu v Azure zvolte typ prostředku, který usnadní zobrazení v seznamu.

2. Vytvoří virtuální počítač. Na Azure Marketplace vyberte požadovaný virtuální počítač a operační systém. Zde je doporučené nastavení:

    - **Enterprise Server:** Vyberte virtuální modul ES2 v3 (s 2 virtuálními procesory a 16 GB paměti) s nainstalovanými Windows Server 2016 a SQL Server 2017. Tato image je dostupná na Azure Marketplace. Enterprise Server může také používat Azure SQL Database.

    - **Enterprise Developer**: Vyberte virtuální mon B2ms (s 2 virtuálními procesory a 8 GB paměti) s nainstalovaným Windows 10 a Visual Studio. Tato image je dostupná na Azure Marketplace.

3. V části **Základy** zadejte své uživatelské jméno a heslo. Vyberte **předplatné** a **umístění/oblast,** kterou chcete použít pro virtuální chod. Vyberte **RGMicroFocusEntServer** pro skupinu prostředků.

4. Vložte oba virtuální počítače do stejné virtuální sítě, aby spolu mohli komunikovat.

5. Přijměte výchozí hodnoty pro zbývající nastavení. Zapamatujte si uživatelské jméno a heslo, které vytvoříte pro správce těchto virtuálních počítače.

6. Po vytvoření virtuálních počítačů otevřete příchozí porty 9003, 86 a 80 pro HTTP a 3389 pro protokol RDP v počítači enterprise server a 3389 v počítači pro vývojáře.

7. Pokud se chcete přihlásit k virtuálnímu počítači podnikového serveru, vyberte na webu Azure Portal virtuální počítač ES2 v3. Přejděte do části **Přehled** a vyberte **Připojit,** chcete-li spustit relaci PRV. Přihlaste se pomocí přihlašovacích údajů, které jste vytvořili pro virtuální hod.

8. Z relace PROTOKOLU RDP načtěte následující dva soubory. Vzhledem k tomu, že používáte systém Windows, můžete soubory přetáhnout do relace protokolu RDP:

    - **es\_40.exe**, instalační soubor podnikového serveru.

    - **mflic**, odpovídající licenční soubor – Podnikový server se bez něj nenačte.

9. Poklepáním na soubor spusťte instalaci. V prvním okně vyberte umístění instalace a přijměte licenční smlouvu s koncovým uživatelem.

     ![Obrazovka Instalace podnikového serveru Micro Focus](media/01-enterprise-server.png)

     Po dokončení instalace se zobrazí následující zpráva:

     ![Obrazovka Instalace podnikového serveru Micro Focus](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Vyhledat aktualizace

Po instalaci zkontrolujte všechny další aktualizace, protože spolu s podnikovým serverem je nainstalována řada požadavků, jako je například rozhraní Microsoft C++ Redistributable a .NET Framework.

### <a name="upload-the-license"></a>Nahrát licenci

1. Spusťte správu licencí micro focus.

2. Klikněte na **Spustit** \> **Správu licencí** **Správce** \> licencí micro Focus a potom klikněte na kartu **Instalovat.** Zvolte typ formátu licence, který chcete nahrát: licenční soubor nebo 16místný licenční kód. Například u souboru v **licenčním souboru**přejděte k souboru **mflic,** který byl dříve odeslán do virtuálního virtuálního soudu, a vyberte **Instalovat licence**.

     ![Dialogové okno Správa licencí micro Focus](media/03-enterprise-server.png)

3. Ověřte, zda se podnikový server načítá. Zkuste spustit web Enterprise Server Administration z <http://localhost:86/> prohlížeče pomocí této adresy URL . Stránka Správa podnikového serveru se zobrazí tak, jak je zobrazena.

     ![Stránka Správa podnikového serveru](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Instalace podnikového vývojáře na vývojářský počítač

1. Vyberte dříve vytvořenou skupinu prostředků (například **RGMicroFocusEntServer**), a pak vyberte bitovou kopii vývojáře.

2. Pokud se chcete přihlásit k virtuálnímu počítači, přejděte do části **Přehled** a vyberte **Připojit**. Toto přihlášení spustí relaci RDP. Přihlaste se pomocí přihlašovacích údajů, které jste vytvořili pro virtuální hod.

3. Z relace PROTOKOLU RDP načtěte následující dva soubory (pokud chcete, přetáhněte je):

    - **edvs2017.exe**, instalační soubor podnikového serveru.

    - **mflic**, odpovídající licenční soubor (Enterprise Developer se bez něj nenačte).

4. Poklepáním na soubor **edvs2017.exe** spusťte instalaci. V prvním okně vyberte umístění instalace a přijměte licenční smlouvu s koncovým uživatelem. Pokud chcete, zvolte **Nainstalovat Rumba 9.5** nainstalovat tento emulátor terminálu, který budete pravděpodobně potřebovat.

     ![Dialogové okno Instalace micro Focus Enterprise Developer for Visual Studio 2017](media/04-enterprise-server.png)

5. Po dokončení instalace se zobrazí následující zpráva:

     ![Zpráva o úspěšnéinstalaci](media/05-enterprise-server.png)

6. Spusťte Správce licencí Micro Focus stejně jako pro podnik Ový server. Zvolte **Spustit** \> **správu** \> licencí Správce **licencí**micro Focus a klikněte na kartu **Instalovat.**

7. Zvolte typ formátu licence, který chcete nahrát: licenční soubor nebo 16místný licenční kód. Například u souboru v **licenčním souboru**přejděte k souboru **mflic,** který byl dříve odeslán do virtuálního virtuálního soudu, a vyberte **Instalovat licence**.

     ![Dialogové okno Správa licencí micro Focus](media/07-enterprise-server.png)

Když se načte enterprise developer, vaše nasazení vývojového a testovacího prostředí Micro Focus v Azure je dokončeno!

## <a name="next-steps"></a>Další kroky

- [Nastavení aplikace Bankovní ukázka](./demo.md)
- [Spuštění podnikového serveru v kontejnerech Dockeru](./run-enterprise-server-container.md)
- [Migrace mainframových aplikací](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
