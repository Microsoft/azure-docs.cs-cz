---
title: Zapnutí zpětného zápisu hesla v Azure AD
description: V tomto kurzu zapnete zpětný zápis hesla, aby se změny hesel iniciované v cloudu přenesly zpět do místní služby Active Directory jako součást Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 5143b59028b3894d3b9a10add594dd91743a4196
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078878"
---
# <a name="tutorial-enabling-password-writeback"></a>Kurz: Povolení zpětného zápisu hesla

V tomto kurzu zapnete zpětný zápis hesla pro vaše hybridní prostředí. Zpětný zápis hesla se používá k synchronizaci změn hesel mezi službou Azure Active Directory (Azure AD) a vaším místním prostředím Active Directory Domain Services (AD DS). Zpětný zápis hesla je součástí služby Azure AD Connect, aby poskytnul zabezpečený mechanismus k odesílání změn v heslech z Azure AD zpět do existujícího místního adresáře. Podrobnější informace o vnitřních mechanismech zpětného zápisu hesla najdete v článku na téma [co je zpětný zápis hesla](concept-sspr-writeback.md).

> [!div class="checklist"]
> * Zapnutí zpětného zápisu hesla ve službě Azure AD Connect
> * Zapnutí zpětného zápisu hesla v prostředí samoobslužného resetování hesla

## <a name="prerequisites"></a>Požadavky

* Přístup k funkčnímu tenantovi Azure AD, který má přiřazenou alespoň zkušební licenci.
* Účet s oprávněními globálního správce v tenantovi Azure AD
* Existující nakonfigurovaný server, na kterém běží aktuální verze [Azure AD Connect](../hybrid/how-to-connect-install-express.md).
* Dokončené předchozí kurzy na téma samoobslužného resetování hesla

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Zapnutí zpětného zápisu hesla ve službě Azure AD Connect

Abyste mohli zpětný zápis hesla zapnout, bude nutné tuto funkci nejprve zapnout ze serveru, na který jste nainstalovali Azure AD Connect.

1. Pokud chcete zpětný zápis hesla nakonfigurovat a zapnout, přihlaste se ke svému serveru Azure AD Connect a spusťte průvodce konfigurací služby **Azure AD Connect**.
2. Na **úvodní** stránce vyberte **Konfigurovat**.
3. Na stránce **Další úlohy** vyberte **Přizpůsobit možnosti synchronizace** a potom vyberte **Další**.
4. Na stránce **Připojení k Azure AD** zadejte přihlašovací údaje globálního správce a pak vyberte **Další**.
5. Na stránkách filtrování **Připojení adresářů** a **Doména či organizační jednotka** vyberte **Další**.
6. Na stránce **Volitelné funkce** vyberte políčko vedle **Zpětný zápis hesla** a vyberte **Další**.
7. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat** a počkejte na dokončení procesu.
8. Až se konfigurace dokončí, vyberte **Ukončit**.

## <a name="enable-password-writeback-option-in-sspr"></a>Zapnutí zpětného zápisu hesla v prostředí samoobslužného resetování hesla

Zapnutí funkce zpětného zápisu hesla ve službě Azure AD Connect je pouze první polovinou postupu. Přístup ke zpětnému zápisu hesla musíte umožnit ještě samoobslužnému resetování hesla, aby uživatelé, kteří si heslo změní nebo resetují, mohli stejné heslo použít i v místním prostředí.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Přejděte do **Azure Active Directory**, klikněte na **Resetování hesla** a potom vyberte **Místní integrace**.
3. U možnosti **Zapisovat hesla zpět do místního adresáře?** nastavte **Ano**.
4. U možnosti **Povolit uživatelům odemčení účtů bez resetování hesel?** také nastavte **Ano**.
5. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zapnuli zpětný zápis hesla pro prostředí samoobslužného resetování hesla. Nechte okno s webem Azure Portal otevřené a pokračujte k dalšímu kurzu, ve kterém nakonfigurujete další nastavení související se samoobslužným resetováním hesla, než toto řešení zavedete v pilotním nasazení.

> [!div class="nextstepaction"]
> [Resetování hesla Azure AD z přihlašovací obrazovky](tutorial-sspr-windows.md)
