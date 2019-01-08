---
title: Technické požadavky pro vytváření imagí virtuálních počítačů pro Azure Marketplace | Dokumentace Microsoftu
description: Pochopili požadavky na vytvoření a nasazení image virtuálního počítače na webu Azure Marketplace pro ostatní uživatele k nákupu.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: 1fdf945645b2d5c89d7f3750ec836595adb90b06
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077075"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Technické požadavky pro vytváření imagí virtuálních počítačů pro Azure Marketplace
Přečtěte si důkladně před zahájením procesu a pochopit, kde a proč provádění jednotlivých kroků. Co je to možné, můžete by měla připravit informace o vaší společnosti a další data, stáhněte potřebné nástroje, nebo vytvořit technickými komponentami před zahájením procesu vytvoření nabídky. Tyto položky by měl být zřejmé z revize v tomto článku.  

## <a name="download-needed-tools--applications"></a>Stáhněte si potřebné nástroje a aplikace
Byste měli mít připravené před zahájením procesu následující položky:

* V závislosti na tom, jaký operační systém cílíte, nainstalujte [rutin prostředí Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) nebo [nástrojů rozhraní příkazového řádku systému Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) z [soubory ke stažení Azure](https://azure.microsoft.com/downloads/) stránky.
* Instalace Průzkumníka služby Azure Storage na webu CodePlex.
* Stáhněte a nainstalujte nástroj Test certifikace pro Azure Certified:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](https://go.microsoft.com/fwlink/?LinkID=526913). Je nutné počítače se systémem Windows ke spuštění certifikačního nástroje. Pokud nemáte k dispozici počítače založené na Windows, můžete spustit nástroj pomocí virtuálního počítače s operačním systémem Windows v Azure.

## <a name="platforms-supported"></a>Podporované platformy
Můžete vyvíjet virtuálních počítačů Azure ve Windows nebo Linuxu. Některé prvky procesu publikování – například vytváření kompatibilní s Azure virtuálního pevného disku (VHD) – pomocí různých nástrojů a kroky, podle toho, jaký operační systém používáte:  

* Pokud používáte Linux, najdete v oddílu "Vytvoření virtuálního pevného disku kompatibilního s Azure (založený na systému Linux)" [Průvodce publikování image virtuálních počítačů](marketplace-publishing-vm-image-creation.md).
* Pokud používáte Windows, najdete v oddílu "Vytvoření virtuálního pevného disku kompatibilního s Azure (založený na Windows)" [Průvodce publikování image virtuálních počítačů](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Budete potřebovat přístup k počítači s Windows založené na:
> 
> * Spusťte nástroj ověření certifikace.
> * Vytvořte sdílený virtuální pevný disk přístup podpis URL pro odeslání certifikace virtuálního pevného disku.
> 
> 

## <a name="develop-your-vhd"></a>Vývoj vašeho virtuálního pevného disku
Můžete vyvíjet virtuálních pevných disků Azure v cloudu nebo lokálně:

* Vývoj pro cloudové znamená, že všechny kroky vývoje provádějí vzdáleně na virtuální pevný disk rezidenční v Azure.
* Místní vývoj vyžaduje stažení virtuálního pevného disku a vývoj pomocí místní infrastruktury. Přestože tento přístup je možné, nedoporučujeme ji. Vývoj pro Windows nebo SQL v místním vyžaduje mít odpovídající místní licenční klíče. Nelze zahrnout nebo po vytvoření virtuálního počítače nainstalovat systém SQL Server. Musíte také základní vaší nabídky na schválené imagi SQL na webu Azure Portal. Pokud se rozhodnete pro vývoj v místním, je nutné provést několik kroků, jinak, než pokud byly vývoje v cloudu. Můžete najít relevantní informace v [vytvořit image virtuálního počítače v místním](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
