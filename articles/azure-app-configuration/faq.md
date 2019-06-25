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
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393355"
---
# <a name="azure-app-configuration-faq"></a>Konfigurace aplikací Azure – nejčastější dotazy

Tento článek se zabývá nejčastější dotazy týkající se konfigurace aplikace pro Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>V čem se App Configuration liší od služby Azure Key Vault?

Konfigurace aplikace je určená pro různé sady případy použití: umožňuje vývojářům spravovat nastavení aplikace a řídit dostupnosti funkce. Cílem je zjednodušit řadu úkolů práce s komplexní konfigurační data.

Konfigurace aplikací podporuje:

- Hierarchické obory názvů
- Používání popisků
- Rozsáhlé dotazů
- Načtení služby batch
- Operace specializované správy
- Správa funkce uživatelského rozhraní

Konfigurace aplikace je pouze doplnění do služby Key Vault a dva byste měli použít ve většině nasazení aplikace vedle sebe.

## <a name="should-i-store-secrets-in-app-configuration"></a>By měl tajné kódy ukládat v konfiguraci aplikací

Přestože konfigurace aplikace poskytuje posílené zabezpečení, Key Vault je stále nejlepší místo pro ukládání tajných klíčů aplikací. Key Vault poskytuje šifrování na úrovni hardwaru, zásady granulární přístup a operace správy, jako je například obměna certifikátů.

## <a name="does-app-configuration-encrypt-my-data"></a>Šifruje konfigurace aplikace pro moje data?

Ano. Konfigurace aplikace šifruje všechny klíčové hodnoty, které obsahuje, a šifruje síťové komunikace. Názvy klíčů slouží jako indexy pro načtení konfigurační data a nejsou šifrovány.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak můžu ukládat konfigurace pro více prostředí (testovací, pracovní, výrobní a tak dále)?

Nyní můžete řídit, kdo má přístup ke konfiguraci aplikací na úrovni za úložiště. Použití samostatné úložiště pro každé prostředí, která vyžaduje jiné oprávnění. Tento přístup nabízí nejlepší izolace zabezpečení.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Co jsou doporučené způsoby, jak používat konfiguraci aplikací?

Zobrazit [osvědčené postupy](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Kolik stojí konfigurace aplikace?

Tato služba je můžete používat ve verzi public preview.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak lze poskytnout návrh nebo nahlásit problém?

Nás můžete kontaktovat přímo na [Githubu](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Další postup

* [Informace o konfiguraci aplikací Azure](./overview.md)
