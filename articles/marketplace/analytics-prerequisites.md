---
title: Požadavky pro programové získání přístupu k analytickým datům
description: Seznamte se s požadavky, které je potřeba splnit, abyste mohli programově přistupovat ke komerčním datům na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3b109048be4a94990c26e31aa5bc2ad36fdd0211
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583734"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Požadavky pro programové získání přístupu k analytickým datům

Než budete moct programově přistupovat ke komerčním datům na webu Marketplace, musíte splnit následující požadavky.

## <a name="commercial-marketplace-enrollment"></a>Registrace komerčního tržiště

Chcete-li získat přístup k datům z komerčního obchodu na webu Marketplace, musíte se zaregistrovat v programu komerčního tržiště a mít účet partnerského centra. Další informace najdete v tématu [Vytvoření účtu komerčního tržiště v partnerském centru](./partner-center-portal/create-account.md).

## <a name="create-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Přihlašovací údaje pro běžné uživatele nelze použít pro programový přístup k datům analýzy komerčního webu Marketplace. Pro přístup k rozhraním API pro analýzu je potřeba vytvořit aplikaci Azure Active Directory (Azure AD) společně s tajným klíčem. Informace o tom, jak vytvořit aplikaci a tajný kód pro Azure AD, najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Přidružte aplikaci Azure AD k tenantovi partnerského centra.

Aplikace Azure AD, kterou jste vytvořili v Azure Portal musí být propojená s vaším účtem partnerského centra. Kroky jsou následující:

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard).
1. V pravém horním rohu vyberte ikonu ozubeného kolečka a pak vyberte **Nastavení účtu**.
1. V nabídce **Nastavení účtu** vyberte **Správa uživatelů**.
1. Vyberte **aplikace Azure AD** a potom vyberte **+ vytvořit aplikaci Azure AD**.
1. Vyberte aplikaci Azure AD, kterou jste vytvořili v Azure Portal a pak vyberte **Další**.
1. Zaškrtněte políčko **správce (Windows)** a pak vyberte **Přidat**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Popisuje stránku vytvořit aplikaci Azure AD pomocí zaškrtávacích políček pro výběr rolí.":::

## <a name="generate-an-azure-ad-token"></a>Generování tokenu Azure AD

Je potřeba vygenerovat token Azure AD pomocí ID aplikace (klienta). Toto ID pomáhá jednoznačně identifikovat vaši klientskou aplikaci na platformě identity Microsoft a tajného klíče klienta z předchozího kroku. Postup pro generování tokenu Azure AD najdete v tématu [volání služby do služby pomocí přihlašovacích údajů klienta (sdílený tajný klíč nebo certifikát)](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow).

> [!NOTE]
> Token je platný po dobu jedné hodiny.

## <a name="next-steps"></a>Další kroky

- [Programové přístupové paradigma](analytics-programmatic-access.md)
