---
title: Příprava certifikátů infrastruktury veřejných klíčů Azure zásobník Azure zásobníku integrované systémy nasazení | Microsoft Docs
description: Popisuje postup přípravy certifikátů PKI zásobník Azure pro Azure zásobníku integrované systémy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203472"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Příprava pro nasazení certifikátů PKI zásobník Azure
Soubory certifikátu [získané z certifikační Autority podle volby](azure-stack-get-pki-certs.md) musí být importovat a exportovat s vlastnostmi odpovídající požadavky na certifikát Azure zásobníku.


## <a name="prepare-certificates-for-deployment"></a>Příprava certifikátů pro nasazení
Pomocí těchto kroků můžete připravit a ověřit certifikáty PKI zásobník Azure: 

### <a name="import-the-certificate"></a>Import certifikátu

1.  Zkopírujte původní verze certifikát [získané z certifikační Autority podle volby](azure-stack-get-pki-certs.md) do adresáře na hostiteli nasazení. 
  > [!WARNING]
  > Nekopírujte soubory, které již byly naimportovány, exportovat nebo změnit žádným způsobem ze souborů poskytovaných certifikační Autoritou.

2.  Klikněte pravým tlačítkem na certifikát a vyberte **nainstalovat certifikát** nebo **instalovat soubor PFX** v závislosti na tom, jak byl doručen certifikátu z certifikační Autority.

3. V **Průvodce importem certifikátu**, vyberte **místního počítače** jako umístění importu. Vyberte **Next** (Další). Na následující obrazovce klikněte na tlačítko Další znovu.

    ![Umístění importu místního počítače](.\media\prepare-pki-certs\1.png)

4.  Zvolte **všechna v následujícím úložišti certifikátů místní** a pak vyberte **Enterprise důvěřovat** jako umístění. Klikněte na tlačítko **OK** zavřete dialogové okno Výběr úložiště certifikátů a pak **Další**.

    ![Konfigurace úložiště certifikátů](.\media\prepare-pki-certs\3.png)

    a. Pokud importujete PFX, zobrazí se další dialogové okno. Na **ochranu privátního klíče** stránky, zadejte heslo pro vaše soubory certifikátů a pak povolte **označit tento klíč jako exportovatelný. To umožňuje zálohování nebo přenos klíčů později** možnost. Vyberte **Next** (Další).

    ![Označit klíč jako exportovatelný](.\media\prepare-pki-certs\2.png)

5. Klikněte na tlačítko Dokončit dokončete import.

### <a name="export-the-certificate"></a>Export certifikátu

Otevřete konzolu MMC Správce certifikátů a připojení k úložišti certifikátů místního počítače.

1. Otevřete konzoli Microsoft Management Console, v systému Windows 10 klikněte pravým tlačítkem na nabídku Start a pak klikněte na tlačítko spustit. Typ **konzoly mmc** klikněte na tlačítko ok.

2. Klikněte na soubor, přidat nebo odebrat modul Snap-In, pak vyberte certifikáty, klikněte na tlačítko Přidat.

    ![Přidat modul Snap-in Certifikáty](.\media\prepare-pki-certs\mmc-2.png)
 
3. Vyberte účet počítače, klikněte na tlačítko Další pak vybrat místní počítač a dokončit. Kliknutím na ok zavřete stránku přidat nebo odebrat modul Snap-In.

    ![Přidat modul Snap-in Certifikáty](.\media\prepare-pki-certs\mmc-3.png)

4. Přejděte do certifikáty > vztah důvěryhodnosti Enterprise > umístění certifikátu. Ověření se váš certifikát na pravé straně.

5. Z konzoly Správce úloh panelu certifikátu vyberte **akce** > **všechny úlohy** > **exportovat**. Vyberte **Next** (Další).

  > [!NOTE]
  > V závislosti na tom, kolik zásobník Azure certifikáty, že máte, můžete chtít tento proces dokončit, více než jednou.

4. Vyberte **Ano, exportovat soukromý klíč**a potom klikněte na **Další**.

5. V části formát souboru pro Export, vyberte **exportovat všechny rozšířené vlastnosti** a pak klikněte na **Další**.

6. Vyberte **heslo** a zadejte heslo pro certifikáty. Heslo si zapamatujte, protože je používán jako parametr nasazení. Vyberte **Next** (Další).

7. Zvolte název souboru a umístění pro export souboru pfx. Vyberte **Next** (Další).

8. Vyberte **Finish** (Dokončit).

## <a name="next-steps"></a>Další postup
[Ověření certifikátů infrastruktury veřejných KLÍČŮ](azure-stack-validate-pki-certs.md)