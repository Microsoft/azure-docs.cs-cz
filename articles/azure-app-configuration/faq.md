---
title: Konfigurace aplikací Azure – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy o konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411893"
---
# <a name="azure-app-configuration-faq"></a>Konfigurace aplikací Azure – nejčastější dotazy

Tento článek adresy časté dotazy týkající se konfigurace aplikace pro Azure.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Čím se liší od služby Key Vault konfigurace aplikace

Konfigurace aplikace je určená pro různé sady případy použití: umožňuje vývojářům spravovat nastavení aplikace a řídit dostupnosti funkce. Cílem je zjednodušit řadu úkolů práce s komplexní konfigurační data. Podporuje hierarchického oboru názvů, označování popisky, rozsáhlé dotazy, načtení služby batch a operace správy specializované a uživatelská rozhraní. Konfigurace aplikace je pouze doplnění do služby Key Vault a dva byste měli použít ve většině nasazení aplikace vedle sebe.

## <a name="should-i-store-secrets-in-app-configuration"></a>By měl tajné kódy ukládat v konfiguraci aplikací

Při konfiguraci aplikace poskytuje posílené zabezpečení, Key Vault je stále nejlepší místo pro ukládání tajných klíčů aplikací. Poskytuje šifrování na úrovni hardwaru, zásady granulární přístup a operace správy, jako je například obměna certifikátů.

## <a name="does-app-configuration-encrypt-my-data"></a>Šifruje konfigurace aplikace pro moje data?

Ano. Konfigurace aplikace šifruje všechny klíčové hodnoty, obsahuje a síťovou komunikaci. Názvy klíčů slouží jako indexy pro načtení konfigurační data a nejsou šifrovány.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Podporuje konfiguraci aplikací pro Azure Virtual Network (VNET)?

Zatím ne. Podpora virtuální sítě je naplánovaná pro obecnou dostupnost.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak můžu ukládat konfigurace pro více prostředí (testovací, pracovní, výrobní a tak dále)?

Nyní můžete řídit, kdo má přístup ke konfiguraci aplikací na úrovni za úložiště. Měli byste použít samostatné úložiště pro každé prostředí, která vyžaduje jiné oprávnění. Tento přístup nabízí nejlepší izolace zabezpečení.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Co jsou doporučené způsoby, jak používat konfiguraci aplikací?

Zobrazit [osvědčené postupy](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Kolik stojí konfigurace aplikace?

Tato služba je můžete používat ve verzi public preview.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak lze poskytnout návrh nebo nahlásit problém?

Nás můžete kontaktovat přímo na [Githubu](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Další postup

* [Informace o konfiguraci aplikací Azure](./overview.md)
