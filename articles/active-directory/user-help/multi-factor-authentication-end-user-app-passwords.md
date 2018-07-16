---
title: Jak používat hesla aplikací v Azure MFA? | Microsoft Docs
description: Tato stránka vám pomůže uživatelům pomoct pochopit, co jsou hesla aplikací a jaké se používají s ohledem na Azure MFA.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: 290458e95aaed0cc85d83539d9d870c334df45df
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059428"
---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Co jsou hesla aplikací v Azure Multi-Factor Authentication?
Některé aplikace nezaložené na prohlížeči, jako je například nativní e-mailovém klientovi Apple, která používá Exchange Active Sync, aktuálně nepodporují ověření službou Multi-Factor Authentication. Ověřování službou Multi-Factor Authentication je povoleno podle uživatelů. To znamená, že pokud uživatel je povolen pro ověřování službou Multi-Factor Authentication a se pokoušíte použít neprohlížečové aplikace, bude to možné. Heslo aplikace. umožňuje to dělo. Pokud jste vynutit ověřování Multi-Factor Authentication pomocí zásad podmíněného přístupu a ne prostřednictvím jednotlivých uživatelů MFA, nebude možné vytvořit hesla aplikací. Aplikace, které používají zásady podmíněného přístupu pro řízení přístupu není nutné hesla aplikací.

Jakmile budete mít heslo aplikace, můžete použít namísto původní heslo se tyto aplikace nezaložené na prohlížeči. Je to proto, že při registraci pro dvoustupňové ověření, budete o tom Microsoft nechcete umožnit všem uživatelům se přihlásit pomocí hesla, pokud nemůžou provést i druhé ověření. Nativní e-mailovém klientovi Apple na váš telefon nemůže přihlásit jako je vzhledem k tomu, že nemůžete žádat k dvoustupňovému ověřování. Řešení pro tento je vytvoření zabezpečenějším heslem aplikace, které nepoužíváte, každodenní, ale pouze pro tyto aplikace, které nemůže zajišťovat podporu pro dvoustupňové ověřování. Používejte heslo aplikace, takže aplikace mohou obejít ověřování Multi-Factor Authentication a nadále fungovat.

> [!NOTE]
> Klienti Office 2013 (včetně Outlooku) podporují nové protokoly pro ověřování a je možné s dvoustupňovým ověřováním.  To znamená, že po povolení hesla aplikací nejsou potřeba pro použití s klientech Office 2013.  Další informace najdete v tématu [Office 2013 moderní ověřování oznámení veřejné Preview verze](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).


## <a name="how-to-use-app-passwords"></a>Jak používat hesla aplikací
Tady jsou některé věci k mějte na paměti, jak používat hesla aplikací.

* Nevytvářejte hesel aplikací. Místo toho že jsou automaticky generovány. Vzhledem k tomu stačí jenom zadat heslo aplikace jedenkrát za aplikace, je bezpečnější používat složitější, automaticky vygenerované heslo místo, které si zapamatujete, vypnutí.
* Aktuálně je omezení 40 hesel na uživatele. Pokud se pokusíte vytvořit po dosažení limitu, vyzve k odstranění některého stávajícího hesla aplikace předtím, než vytvoříte nové.
* Měli byste použít heslo aplikace, která je jedno zařízení, nikoliv podle aplikací. Můžete třeba vytvořit jedno heslo aplikace pro přenosný počítač a používat takové heslo aplikace pro všechny aplikace v tomto přenosném počítači. Potom vytvořte druhý heslo aplikace pro všechny vaše aplikace na ploše.
* Při první registraci pro dvoustupňové ověřování, jsou uvedeny jedno heslo aplikace.  Pokud potřebujete další značky, můžete je vytvořit.



## <a name="creating-and-deleting-app-passwords"></a>Vytváření a odstraňování hesla aplikací
Během počáteční přihlášení budete mít, můžete použít heslo aplikace.  Kromě toho můžete také vytvářet a odstraňovat hesla aplikací později.  Postup závisí na tom, jak používat ověřování Multi-Factor Authentication. Odpovězte na následující otázky k určení, kde by měl přejít ke správě hesel aplikací:

1. Používáte dvoustupňové ověřování pro svůj osobní účet Microsoft? Pokud ano, by měla odkazovat na [hesla aplikací a dvoustupňové ověřování](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification) článek o pomoc. Pokud ne, i nadále otázka, na dva.

2. OK, takže použít dvoustupňové ověřování pro váš pracovní nebo školní účet. Se používá k přihlašování k aplikacím Office 365? Pokud ano, by měla odkazovat na [vytvořit heslo aplikace pro Office 365](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183) nápovědu. Pokud ne, i nadále tři otázky.

3. Používáte dvoustupňové ověřování s využitím Microsoft Azure? Pokud ano, pokračovat [spravovat hesla aplikací na webu Azure Portal](#manage-app-passwords-in-the-Azure-portal) části tohoto článku. Pokud ne, i nadále čtvrtou otázku.

4. Nejste si jisti, ve kterém můžete pomocí dvoustupňové ověřování? Dál [spravovat hesla aplikací pomocí portálu MyApps](#manage-app-passwords-with-the-myapps-portal) části tohoto článku.


## <a name="manage-app-passwords-in-the-azure-portal"></a>Správa hesel aplikací na webu Azure Portal
Pokud používáte dvoustupňové ověřování s Azure, budete chtít vytvořit hesla aplikací na webu Azure portal.



## <a name="manage-app-passwords-with-the-myapps-portal"></a>Správa hesel aplikací pomocí portálu MyApps.
Pokud si nejste jisti, jak pomocí služby Multi-Factor authentication, pak můžete vždy vytvořit a odstranit hesla aplikací prostřednictvím portálu myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Chcete-li vytvořit heslo aplikace pomocí portálu MyApps
1. Přihlaste se k [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Klikněte na své jméno v pravém horním rohu a vyberte **profilu**.
3. Vyberte **dalšího ověření zabezpečení**.
   ![Vyberte další bezpečnostní ověření – snímek obrazovky](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Vyberte **hesla aplikací**.
   ![Vyberte hesel aplikací – snímek obrazovky](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klikněte na možnost **Vytvořit**.
6. Zadejte název hesla aplikace a klikněte na tlačítko **Další**.
7. Kopírovat heslo aplikace do schránky a vložte ho do vaší aplikace.
   ![Vytvořit heslo aplikace](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Chcete-li odstranit heslo aplikace pomocí portálu MyApps
1. Přihlaste se k [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. V horní části stránky vyberte profil.
3. Vyberte **dalšího ověření zabezpečení**.

   ![Vyberte další bezpečnostní ověření – snímek obrazovky](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Vyberte **hesla aplikací**.

   ![Vyberte hesel aplikací – snímek obrazovky](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Vedle heslo aplikace, kterou chcete odstranit, klikněte na tlačítko **odstranit**.

   ![Odstranit heslo aplikace.](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. Potvrďte, že chcete odstranit toto heslo kliknutím **Ano**.
7. Při odstranění hesla aplikace, můžete kliknout na **zavřete**.

## <a name="next-steps"></a>Další postup

- [Správa nastavení dvoustupňové ověření](multi-factor-authentication-end-user-manage-settings.md)

- Vyzkoušejte si [aplikaci Microsoft Authenticator](microsoft-authenticator-app-how-to.md) ověření přihlášení s oznámení aplikací, místo příjem textů nebo volání.
