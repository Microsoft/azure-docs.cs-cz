---
title: Použití nástroje MSIXMGR – Azure
description: Jak používat nástroj MSIXMGR pro virtuální počítače s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744681"
---
# <a name="using-the-msixmgr-tool"></a>Použití nástroje MSIXMGR

Nástroj MSIXMGR slouží k rozbalování aplikací zabaleného MSIX do MSIX obrázků. Nástroj přebírá MSIX aplikaci (. MSIX) a rozbalí ho do souboru VHD, VHDx nebo CIM. Výsledná image MSIX je uložená v účtu Azure Storage, který používá nasazení virtuálních počítačů s Windows. Tento článek vám ukáže, jak používat nástroj MSIXMGR.

>[!NOTE]
>Aby se zajistila kompatibilita, ujistěte se, že CIMs ukládání imagí MSIX se generuje na verzi operačního systému, kterou spouštíte ve fondech hostitelů virtuálních počítačů s Windows. MSIXMGR může vytvářet soubory CIM, ale tyto soubory můžete použít jenom s fondem hostitelů se systémem Windows 10 20H2.

## <a name="requirements"></a>Požadavky

Než budete moct postupovat podle pokynů v tomto článku, musíte provést následující akce:

- [Stažení nástroje MSIXMGR](https://aka.ms/msixmgr)
- Získat MSIX aplikaci (. Soubor MSIX)
- Získat oprávnění správce na počítači, kde vytvoříte bitovou kopii MSIX

## <a name="create-an-msix-image"></a>Vytvoření image MSIX

Rozšíření je proces přebírání zabalených aplikací v MSIX (. MSIX) a rozzipovává ho do obrázku MSIX (. VHD (x) nebo. Soubor CIM).

Postup rozšíření souboru MSIX:

1. Pokud jste to ještě neudělali, [Stáhněte si nástroj MSIXMGR](https://aka.ms/msixmgr) .

2. Dekomprimovat MSIXMGR.zip do místní složky.

3. Otevřete příkazový řádek v režimu zvýšené úrovně.

4. Vyhledejte místní složku z kroku 2.

5. Spuštěním následujícího příkazu na příkazovém řádku vytvořte obrázek MSIX.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Nezapomeňte nahradit hodnoty zástupných symbolů odpovídajícími hodnotami. Například:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Teď, když jste vytvořili image, přejdete do cílové složky a ujistěte se, že jste úspěšně vytvořili image MSIX (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Vytvoření MSIX obrázku v souboru CIM

Pomocí příkazu v [kroku 5](#create-an-msix-image) můžete také vytvořit soubory CIM a VHDX nahrazením typu souboru a cílové cesty.

Tady je příklad, jak pomocí tohoto příkazu vytvořit soubor CIM:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Tady je postup, jak pomocí tohoto příkazu vytvořit disk VHDX:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Další kroky

Další informace o připojení aplikace MSIX v souvislosti s [připojením aplikace MSIX?](what-is-app-attach.md)

Informace o tom, jak nastavit připojení aplikace, najdete v těchto článcích:

- [Nastavení připojení aplikace MSIX s využitím webu Azure Portal](app-attach-azure-portal.md)
- [Nastavení připojení aplikace MSIX pomocí PowerShellu](app-attach-powershell.md)
- [Vytváření skriptů PowerShellu pro připojení aplikace MSIX](app-attach.md)
- [Příprava image MSIX pro virtuální počítače s Windows](app-attach-image-prep.md)
- [Nastavení sdílení souborů pro připojení aplikace MSIX](app-attach-file-share.md)

Pokud máte dotazy týkající se připojení aplikace MSIX, přečtěte si článek připojení k aplikaci – [Nejčastější dotazy](app-attach-faq.md) a [Glosář k aplikaci](app-attach-glossary.md).
