---
title: Postup přidání technických podrobností pro nabídku SaaS v partnerském centru Microsoftu
description: Naučte se, jak poskytnout technické údaje pro nabídku software jako službu (SaaS) pro komerční tržiště Microsoftu.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 28c0be40387f411286230f94c19fa23a80e650af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746399"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Postup přidání technických podrobností pro nabídku SaaS

Tento článek popisuje, jak zadat technické údaje, které vám pomůžou připojit se k vašemu řešení Microsoftu pro komerční tržišti. Toto připojení nám umožní zřídit vaši nabídku pro zákazníka, pokud se rozhodne získat a spravovat. Další podrobnosti o těchto nastaveních najdete v tématu [technické informace](plan-saas-offer.md#technical-information).

> [!NOTE]
> Pokud se rozhodnete zpracovávat transakce nezávisle, tato možnost se nezobrazí. Místo toho přejděte k tomu, [Jak prodávat nabídku SaaS](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Technická konfigurace

Na kartě **Technická konfigurace** definujete technické údaje, které komerční tržiště používá ke komunikaci s vaší aplikací nebo řešením SaaS. 

- **Adresa URL cílové stránky** (požadováno) – definujte adresu URL webu SaaS (například `https://contoso.com/signup` ), kterou si zákazníci vyjdou po získání vaší nabídky z komerčního tržiště a aktivaci procesu konfigurace z nově vytvořeného předplatného SaaS.

  > [!IMPORTANT]
  > Vaše cílová stránka by měla být spuštěná a musí běžet 24/7. Toto je jediný způsob, jakým se dozvíte o nových nákupech nabídek SaaS provedených na komerčním tržišti nebo o požadavcích na konfiguraci aktivního předplatného nabídky.

- **Webhook připojení** (povinné) – pro všechny asynchronní události, které Microsoft potřebuje odeslat (například předplatné SaaS se zrušilo), budeme potřebovat zadat adresu URL Webhooku připojení. Tuto adresu URL budeme zavolat, abychom vás na událost informovali.

  > [!IMPORTANT]
  > Webhook by měl být v provozu a musí běžet 24/7, protože se jedná o jediný způsob, jakým se dozvíte o aktualizacích předplatných SaaS vašich zákazníků, které se kupují prostřednictvím webu Comercial Marketplace.

- **Azure Active Directory ID tenanta** (povinné) – Chcete-li zjistit ID klienta pro vaši aplikaci Azure Active Directory (Azure AD), v Azure Active Directory klikněte na okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . Ve sloupci **Zobrazovaný název** vyberte aplikaci. Pak vyhledejte číslo **ID adresáře (tenant)** v seznamu (například `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory ID aplikace** (povinné) – Chcete-li zjistit [ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), v Azure Active Directory klikněte na okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . Ve sloupci **Zobrazovaný název** vyberte aplikaci. Pak vyhledejte číslo ID aplikace (klienta) uvedené (například `50c464d3-4930-494c-963c-1e951d15360e` ).

Než budete pokračovat na další kartu, vyberte **Uložit koncept** . Přehled plánu:

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit plány pro nabídku SaaS](create-new-saas-offer-plans.md)