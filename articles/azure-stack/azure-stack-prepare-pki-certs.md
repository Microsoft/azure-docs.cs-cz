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
ms.openlocfilehash: c195cc0bacd9eea7e75fa35cd155845f03dd21cf
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Příprava pro nasazení certifikátů PKI zásobník Azure
Soubory certifikátu [získané z certifikační Autority podle volby](azure-stack-get-pki-certs.md) musí být importovat a exportovat s vlastnostmi odpovídající požadavky na certifikát Azure zásobníku.


## <a name="prepare-certificates-for-deployment"></a>Příprava certifikátů pro nasazení
Pomocí těchto kroků můžete připravit a ověřit certifikáty PKI zásobník Azure: 

1.  Zkopírujte původní verze certifikát [získané z certifikační Autority podle volby](azure-stack-get-pki-certs.md) do adresáře na hostiteli nasazení. 
  > [!WARNING]
  > Nekopírujte soubory, které již byly naimportovány, exportovat nebo změnit žádným způsobem ze souborů poskytovaných certifikační Autoritou.

2.  Importujte certifikátů do úložiště certifikátů místního počítače:

    a.  Klikněte pravým tlačítkem na certifikát a vyberte **instalovat soubor PFX**.

    b.  V **Průvodce importem certifikátu**, vyberte **místního počítače** jako umístění importu. Vyberte **Next** (Další).

    ![Umístění importu místního počítače](.\media\prepare-pki-certs\1.png)

    c.  Vyberte **Další** na **vybrat soubor k importu** stránky.

    d.  Na **ochranu privátního klíče** stránky, zadejte heslo pro vaše soubory certifikátů a pak povolte **označit tento klíč jako exportovatelný. To umožňuje zálohování nebo přenos klíčů později** možnost. Vyberte **Next** (Další).

    ![Označit klíč jako exportovatelný](.\media\prepare-pki-certs\2.png)

    e.  Zvolte **všechna v následujícím úložišti certifikátů místní** a pak vyberte **Enterprise důvěřovat** jako umístění. Klikněte na tlačítko **OK** zavřete dialogové okno Výběr úložiště certifikátů a pak **Další**.

    ![Konfigurace úložiště certifikátů](.\media\prepare-pki-certs\3.png)

  f.    Klikněte na tlačítko **Dokončit** k dokončení Průvodce importem certifikátu.

  g.    Opakujte postup pro všechny certifikáty, které zadáte pro vaše nasazení.

3. Exportujte certifikátu do formátu souboru PFX s požadavky na Azure zásobníku:

  a.    Otevřete konzolu MMC Správce certifikátů a připojení k úložišti certifikátů místního počítače.

  b.    Přejděte na **Enterprise důvěřovat** adresáře.

  c.    Vyberte jeden z certifikátů, které jste importovali v kroku 2 výše.

  d.    Z konzoly Správce úloh panelu certifikátu vyberte **akce** > **všechny úlohy** > **exportovat**.

  e.    Vyberte **Next** (Další).

  f.    Vyberte **Ano, exportovat soukromý klíč**a potom klikněte na **Další**.

  g.    V části formát souboru pro Export, vyberte **exportovat všechny rozšířené vlastnosti** a pak klikněte na **Další**.

  h.    Vyberte **heslo** a zadejte heslo pro certifikáty. Heslo si zapamatujte, protože je používán jako parametr nasazení. Vyberte **Next** (Další).

  i.    Zvolte název souboru a umístění pro export souboru pfx. Vyberte **Next** (Další).

  j.    Vyberte **Finish** (Dokončit).

  k.    Tento postup opakujte pro všechny certifikáty, které jste naimportovali pro vaše nasazení v kroku 2 výše.

## <a name="next-steps"></a>Další postup
[Ověření certifikátů infrastruktury veřejných KLÍČŮ](azure-stack-validate-pki-certs.md)