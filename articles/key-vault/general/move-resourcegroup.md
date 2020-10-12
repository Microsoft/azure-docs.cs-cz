---
title: Azure Key Vault přesunu trezoru do jiné skupiny prostředků | Microsoft Docs
description: Pokyny k přesunutí trezoru klíčů do jiné skupiny prostředků.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: fe8051d551077666c06ac033f22303fd643ac602
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585729"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Přesunutí Azure Key Vault mezi skupinami prostředků

## <a name="overview"></a>Přehled

Přesun trezoru klíčů mezi skupinami prostředků je podporovaná funkce trezoru klíčů. Přesunutí trezoru klíčů mezi skupinami prostředků nebude mít vliv na bránu firewall trezoru klíčů ani na konfigurace zásad přístupu. Připojené aplikace a instanční objekty by měly dál fungovat tak, jak mají.

## <a name="design-considerations"></a>Aspekty návrhu

Vaše organizace možná implementovala Azure Policy s vynucením nebo vyloučením na úrovni skupiny prostředků. V této skupině prostředků může být jiná sada přiřazení zásad, ve které aktuálně existuje Trezor klíčů a skupina prostředků, do které přesouváte Trezor klíčů. Konflikt v požadavcích zásad má potenciál pro přerušení vašich aplikací.

### <a name="example"></a>Příklad

Máte aplikaci připojenou k trezoru klíčů, která vytváří certifikáty platné po dobu dvou let. Skupina prostředků, ve které se pokoušíte přesunout Trezor klíčů, má přiřazení zásady, které blokuje vytváření certifikátů, které jsou platné po dobu delší než jeden rok. Po přesunutí trezoru klíčů do nové skupiny prostředků bude operace vytvoření certifikátu, který je platný po dobu dvou let, blokována přiřazením zásad Azure.

### <a name="solution"></a>Řešení

Ujistěte se, že přejdete na stránku Azure Policy na Azure Portal a podíváte se na přiřazení zásad pro vaši aktuální skupinu prostředků a na skupinu prostředků, na kterou přesouváte, a zajistěte, aby nedocházelo k žádnému neshodě.

## <a name="procedure"></a>Postup

1. Přihlášení k webu Azure Portal
2. Přejděte do trezoru klíčů.
3. Klikněte na kartu Přehled.
4. Vyberte tlačítko přesunout.
5. Z možností rozevíracího seznamu vyberte přesunout do jiné skupiny prostředků.
6. Vyberte skupinu prostředků, do které chcete přesunout Trezor klíčů.
7. Potvrzení upozornění týkající se přesunu prostředků
8. Vyberte OK.

Key Vault teď vyhodnotí platnost přesunutí prostředku a upozorní vás na případné chyby. Pokud se nenaleznou žádné chyby, přesun prostředku se dokončí. 
