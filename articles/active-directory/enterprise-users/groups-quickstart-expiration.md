---
title: Rychlý Start pro zásady vypršení platnosti skupiny – Azure AD | Microsoft Docs
description: Vypršení platnosti pro Microsoft 365 skupiny – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0b9e4ccd595e90ab941cb061a016f8c4d4f3d0
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503022"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Rychlý Start: nastavení Microsoft 365ch skupin, jejichž platnost vyprší v Azure Active Directory

V tomto rychlém startu nastavíte zásady vypršení platnosti pro vaše Microsoft 365 skupiny. Když mají uživatelé možnost nastavovat vlastní skupiny, můžou se začít množit nepoužívané skupiny. Jedním ze způsobů, jak spravovat nepoužívané skupiny, je nastavit vypršení platnosti těchto skupin a tím snížit potřebu údržby související s ručním odstraňováním skupin.

Zásady vypršení platnosti je jednoduchá:

- Skupiny s aktivitami uživatele se automaticky Obnovují jako vypršení platnosti v blízkosti
- Vlastníci skupiny obdrží oznámení o potřebě prodloužit platnost skupiny, které vyprší platnost.
- Skupina, které se neprodlouží platnost, se odstraní.
- Odstraněnou skupinu Microsoft 365 lze obnovit do 30 dnů vlastníkem skupiny nebo správcem služby Azure AD.

> [!NOTE]
> Skupiny teď používají Azure AD Intelligence k automatickému obnovení na základě toho, jestli jsou v nedávném použití. Toto rozhodnutí o obnovení je založené na aktivitě uživatelů ve skupinách napříč Microsoft 365 službami, jako je Outlook, SharePoint, teams, Yammer a další.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisite"></a>Požadavek

 Minimální privilegovaná role požadovaná k nastavení vypršení platnosti skupiny je správcem uživatele v organizaci.

## <a name="turn-on-user-creation-for-groups"></a>Zapnutí vytváření uživatelů pro skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele.

2. Vyberte **Skupiny** a pak vyberte **Obecné**.
  
   ![Stránka nastavení samoobslužné skupiny](./media/groups-quickstart-expiration/self-service-settings.png)

3. Nastavení  **Uživatelé mohou vytvořit Microsoft 365 skupiny** na **Ano**.

4. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení skupin.

## <a name="set-group-expiration"></a>Nastavení vypršení platnosti skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com), vyberte **Azure Active Directory**  >  **skupiny**  >  **vypršení platnosti** a otevřete nastavení vypršení platnosti.
  
   ![Stránka nastavení vypršení platnosti pro skupinu](./media/groups-quickstart-expiration/expiration-settings.png)

2. Nastavte interval vypršení platnosti. Vyberte přednastavenou hodnotu nebo zadejte vlastní hodnotu vyšší než 31 dnů. 

3. Zadejte e-mailovou adresu, kam se mají odesílat oznámení o vypršení platnosti v případě, že skupina nemá žádného vlastníka.

4. Pro tento rychlý Start nastavte **možnost Povolit vypršení platnosti pro tyto Microsoft 365 skupiny** na **vše**.

5. Jakmile budete hotovi, výběrem možnosti **Uložit** uložte nastavení vypršení platnosti.

A to je vše! V tomto rychlém startu jste úspěšně nastavili zásady vypršení platnosti pro vybrané skupiny Microsoft 365.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="to-remove-the-expiration-policy"></a>Odebrání zásady vypršení platnosti

1. Ujistěte se, že jste k [Azure Portal](https://portal.azure.com) přihlášeni pomocí účtu, který je globálním správcem vaší organizace Azure AD.
2. Vyberte **Azure Active Directory**  >  **skupiny**  >  **vyprší**.
3. **U těchto Microsoft 365 skupin nastavte možnost Povolit vypršení platnosti** na **žádná**.

### <a name="to-turn-off-user-creation-for-groups"></a>Vypnutí vytváření uživatelů pro skupiny

1. Vyberte **Azure Active Directory**  >  **skupiny**  >  **Obecné**. 
2. Nastavení **Uživatelé můžou na portálech Azure vytvářet skupiny Microsoft 365** na **ne**.

## <a name="next-steps"></a>Další kroky

Další informace o vypršení platnosti včetně pokynů PowerShellu a technických omezení najdete v následujícím článku:

> [!div class="nextstepaction"]
> [PowerShell – zásady vypršení platnosti](groups-lifecycle.md)
