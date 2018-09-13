---
title: Zveřejnění položky marketplace vlastní ve službě Azure Stack (operátor cloudu) | Dokumentace Microsoftu
description: Jako operátory Azure stacku zjistěte, jak publikovat vlastní marketplace položku ve službě Azure Stack.
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
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: c16d8a282d489e7a2b5ee9908f52224aea6118d6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713387"
---
# <a name="the-azure-stack-marketplace-overview"></a>Přehled Azure Stack Marketplace

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Na webu Marketplace je kolekce služeb, aplikací a prostředků, které jsou přizpůsobené pro službu Azure Stack. Prostředky patří sítě, virtuální počítače, úložiště a tak dále. Uživatelé jsou zde vytvářet nové prostředky a nasazení nových aplikací. Si ho představit jako nákupní katalog, ve kterém uživatelé mohou procházet a vyberte položky, které chtějí používat. Pokud chcete použít položky Marketplace, musí uživatelé přihlásit k nabídky, která jim uděluje přístup k položce.

Jako operátory Azure stacku, rozhodnete položky, které chcete přidat (publikování) na webu Marketplace. Můžete publikovat věci, jako je databáze, App Services a tak dále. Publikování jsou viditelné všem uživatelům. Můžete publikovat vlastní položky, které vytvoříte. Můžete také publikovat položky z stále se rozšiřující [seznam položek z Azure Marketplace](azure-stack-marketplace-azure-items.md). Při publikování položky Marketplace, uživatelé je můžou zobrazit během pěti minut.

> [!Caution]  
> Všechny artefakty položky galerie označovaný jako Image a soubory json jsou přístupné bez ověřování po provedení dostupných v Tržišti Azure Stack. Další aspekty při publikování položky marketplace vlastní, naleznete v tématu [vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Chcete-li otevřít na webu Marketplace, v konzole pro správu vyberte **+ vytvořit prostředek**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Položky Marketplace
Položka Azure Stack Marketplace je služby, aplikace nebo prostředku, které uživatelé můžou stáhnout a použít. Všechny položky Azure Marketplace zásobníku jsou viditelné pro všechny uživatele, včetně správy různé věci, třeba plány a nabídky. Tyto položky nevyžadují předplatné zobrazení, ale jsou uživatelům nefunkční.

Má každá položka Marketplace:

* Šablonu Azure Resource Manageru pro zřizování prostředků
* Metadata, jako jsou řetězce, ikony a další marketingové materiály
* Informace o formátování, aby se zobrazila položka na portálu

Všechny položky na webu Marketplace publikovat ve formátu balíčku Galerie Azure (.azpkg). Přidat nasazení nebo modul runtime prostředky (jako je kód, soubory zip s softwaru nebo Image virtuálních počítačů) do služby Azure Stack samostatně, nikoli jako součást položky Marketplace. 

Verze 1803 nebo novější Azure Stack imagí při převádí na zhuštěné soubory stáhnou z Azure nebo při nahrání vlastních imagí. Tento postup přidá čas při přidávání obrázku, ale šetří místo a urychluje nasazení těchto imagí. Převod platí jenom pro nové Image.  Existující Image se nezmění. 

## <a name="next-steps"></a>Další postup
[Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)

