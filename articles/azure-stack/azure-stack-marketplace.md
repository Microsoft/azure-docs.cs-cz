---
title: Publikovat vlastní marketplace položky v Azure zásobníku (operátor cloudu) | Microsoft Docs
description: Jako operátor zásobník Azure zjistěte, jak publikovat vlastní marketplace položky v Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 4ea23ed01e6432f24024d7e8cc07c2dfe42ac639
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605565"
---
# <a name="the-azure-stack-marketplace-overview"></a>Přehled Azure Marketplace zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Na webu Marketplace je kolekce služeb, aplikací a prostředky, které jsou přizpůsobené pro Azure zásobníku. Prostředky zahrnují sítě, virtuální počítače, úložiště a tak dále. Uživatelé se zde k vytvoření nových prostředků a nasazení nové aplikace. Považujte ho za nákupní katalogu, kde uživatelé mohou procházet a vyberte položky, které chtějí používat. Pokud chcete použít položku Marketplace, musí uživatelé přihlásit k nabídka, která jim udělí přístup k položce.

Jako operátor zásobník Azure, rozhodnete položek, které chcete přidat (publikovat) na webu Marketplace. Můžete publikovat třeba databáze, aplikační služby a tak dále. Umožňuje publikování je viditelná pro všechny uživatele. Můžete publikovat vlastní položky, které vytvoříte. Můžete také publikovat položky z rozšiřujících se [seznam položek Azure Marketplace](azure-stack-marketplace-azure-items.md). Při publikování položky Marketplace, uživatelé mohou vidět do pěti minut.

> [!Caution]  
> Všechny artefakty položky galerie známé jako bitových kopií a soubory json jsou přístupné bez ověření po zpřístupňuje je v zásobníku Azure marketplace. Další aspekty při publikování položky vlastní marketplace, najdete v části [vytvoření a publikování položku Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Otevřete na Marketplace, v konzole pro správu vyberte **nový**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Položky Marketplace
Položka Azure Marketplace zásobníku je služby, aplikace nebo prostředku, který vaši uživatelé můžete stáhnout a použít. Všechny položky Marketplace zásobník Azure jsou viditelné pro všechny uživatele, včetně správy věci, jako je plány a nabídky. Tyto položky nevyžadují předplatné zobrazení, ale jsou uživatelům funkční.

Každá položka Marketplace má:

* Šablonu Azure Resource Manageru pro zřizování prostředků
* Metadata, jako jsou řetězce, ikony a další marketingové nástroje
* Formátování informace o položce v portálu

Každá položka publikovaných na Marketplace s cílem používá formát balíčku Galerie Azure (.azpkg). Přidat nasazení nebo modul runtime prostředky (jako je kód, soubory zip s softwaru nebo bitové kopie virtuálních počítačů) do zásobník Azure samostatně, nikoli jako součást položku Marketplace. 

S verzí 1803 a novější zásobník Azure převede bitové kopie na zhuštěných souborů při stažení z Azure nebo když nahrát vlastní Image. Tento proces prodlužuje čas při přidávání obrazu, ale šetří místo a urychluje nasazení těchto bitových kopií. Převod pouze u nových bitových kopií.  Existujících bitových kopií, nebudou změněny. 

## <a name="next-steps"></a>Další postup
[Stažení položky Marketplace.](azure-stack-download-azure-marketplace-item.md)  
[Vytvoření a publikování položku Marketplace.](azure-stack-create-and-publish-marketplace-item.md)

