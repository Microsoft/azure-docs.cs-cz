---
title: 'Azure Active Directory Domain Services: Zásady hesel | Microsoft Docs'
description: Principy zásad hesel ve spravovaných doménách
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 30f4558339bbfddd2296cd1cb918c6ef8999b67e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879194"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zásady hesel a uzamčení účtů ve spravovaných doménách
Tento článek popisuje výchozí zásady hesel ve spravované doméně. Také se zabývá tím, jak můžete tyto zásady nakonfigurovat.

## <a name="fine-grained-password-policies-fgpp"></a>Podrobné zásady pro hesla (podrobné zásady)
Pomocí podrobných zásad pro hesla můžete v jedné doméně určit mnoho zásad hesel. Podrobné zásady umožňuje použít různá omezení zásad hesel a uzamčení účtů pro různé skupiny uživatelů v doméně. Můžete například použít striktní nastavení hesla pro privilegované účty.

Pomocí podrobné zásady můžete nakonfigurovat následující nastavení hesla:
* Minimální délka hesla
* Historie hesel
* Hesla musí splňovat požadavky na složitost.
* Minimální stáří hesla
* Maximální stáří hesla
* Zásady uzamčení účtů
    * Doba trvání uzamčení účtu
    * Počet povolených neúspěšných pokusů o přihlášení
    * Resetovat počet neúspěšných pokusů o přihlášení za
    
Podrobné zásady má vliv jenom na uživatele vytvořené přímo v Azure služba AD DS. Uživatelé cloudu a uživatelé domény synchronizovaný do spravované domény Azure služba AD DS v Azure AD nejsou ovlivněné nastavením složitosti hesla. Podrobné zásady se distribuuje prostřednictvím přidružení skupin ve spravované doméně Azure služba AD DS a veškeré změny, které provedete, se uplatní při přihlášení dalšího uživatele. Změna zásad neodemkne uživatelský účet, který je už uzamčený.

## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Výchozí jemně odstupňovaná nastavení zásad pro hesla ve spravované doméně
Následující snímek obrazovky znázorňuje výchozí jemně odstupňované zásady hesel nakonfigurované na Azure AD Domain Services spravované doméně.

![Výchozí podrobné zásady pro hesla](./media/how-to/default-fgpp.png)

> [!NOTE]
> Nemůžete upravit ani odstranit výchozí integrované zásady pro hesla. Členové skupiny Správci AAD DC můžou vytvořit vlastní podrobné zásady a nakonfigurovat je tak, aby přepsali (mají přednost před) výchozí vestavěnou podrobné zásady.
>
>

## <a name="password-policy-settings"></a>Nastavení zásad hesel
Ve spravované doméně jsou ve výchozím nastavení nakonfigurované následující zásady hesel:
* Minimální délka hesla (ve znacích): 7
* Maximální stáří hesla (doba života): 90 dní
* Hesla musí splňovat požadavky na složitost.

### <a name="account-lockout-settings"></a>Nastavení uzamčení účtu
Ve spravované doméně jsou ve výchozím nastavení nakonfigurované následující zásady uzamčení účtů:
* Doba trvání uzamčení účtu: 30
* Počet povolených neúspěšných pokusů o přihlášení: 5
* Resetovat počet neúspěšných pokusů o přihlášení po: 30 minut

V případě, že se pět neplatných hesel používá během 2 minut, jsou uživatelské účty uzamčeny po dobu 30 minut. Účty se po 30 minutách automaticky odemkní.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Vytvoření vlastních podrobných zásad pro hesla (podrobné zásady) ve spravované doméně
Můžete vytvořit vlastní podrobné zásady a použít ho na konkrétní skupiny ve spravované doméně. Tato konfigurace efektivně přepisuje výchozí podrobné zásady nakonfigurovaný pro spravovanou doménu.

> [!TIP]
> Pouze členové skupiny **AAD DC Administrators** mají oprávnění vytvářet vlastní podrobné zásady pro hesla.
>
>

Kromě toho můžete také vytvořit vlastní podrobné zásady pro hesla a použít je na jakékoli vlastní organizační jednotky, které vytvoříte ve spravované doméně.

Vlastní podrobné zásady můžete nakonfigurovat z následujících důvodů:
* Chcete nastavit jiné zásady uzamčení účtů.
* Pro konfiguraci výchozího nastavení životnosti hesla pro spravovanou doménu.

Vytvoření vlastního podrobné zásady ve spravované doméně:
1. Přihlaste se k virtuálnímu počítači s Windows, který používáte ke správě spravované domény (musí být minimálně Windows Server 2012 R2). Pokud ho nemáte, postupujte podle pokynů pro [správu domény Azure AD Domain Services](manage-domain.md).
2. Na virtuálním počítači spusťte **Centrum správy služby Active Directory** .
3. Klikněte na název domény (například ' contoso100.com ').
4. Dvojím kliknutím na **systém** otevřete kontejner systému.
5. Dvakrát klikněte na **kontejner nastavení hesel**.
6. Zobrazí se výchozí integrovaná podrobné zásady pro spravovanou doménu s názvem **AADDSSTFPSO**. Tuto vestavěnou podrobné zásady nejde upravit. Můžete ale vytvořit nové vlastní podrobné zásady a přepsat výchozí podrobné zásady.
7. Na panelu **úlohy** vpravo klikněte na **Nový** a pak na **Nastavení hesla**.
8. V dialogovém okně **vytvořit nastavení hesla** zadejte vlastní nastavení hesla, které se má použít jako součást vlastního podrobné zásadyu. Nezapomeňte nastavit prioritu odpovídajícím způsobem, aby bylo možné přepsat výchozí podrobné zásady.

   ![Vytvořit vlastní podrobné zásady](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Nezapomeňte zrušit volbu Možnosti chránit před náhodným odstraněním.** Pokud je vybraná tato možnost, podrobné zásady se nedá Uložit.
   >
   >

9. V části **přímo platí pro**klikněte na tlačítko **Přidat** . V dialogovém okně **Vybrat uživatele nebo skupiny** klikněte na tlačítko **umístění** .

   ![Vyberte uživatele a skupiny](./media/how-to/fgpp-applies-to.png)

10. V dialogovém okně **umístění** rozbalte název domény a klikněte na **AADDC uživatelé**. Teď můžete vybrat skupinu z vestavěných organizačních jednotek uživatelů, na které se má podrobné zásady použít.

    ![Vyberte organizační jednotku, ke které patří skupina.](./media/how-to/fgpp-container.png)

11. Zadejte název skupiny a kliknutím na tlačítko **Kontrola názvů** ověřte, zda skupina existuje.

    ![Vyberte skupinu, kterou chcete použít podrobné zásady](./media/how-to/fgpp-apply-group.png)

12. Název skupiny se zobrazí v části **přímo platí pro** oddíl. Kliknutím na tlačítko **OK** uložte tyto změny.

    ![Podrobné zásady použito](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Použití vlastních zásad hesla pro uživatelské účty ve vlastní organizační jednotce:** Podrobné zásady pro hesla se dají použít jenom pro skupiny. Pokud chcete nakonfigurovat vlastní zásady pro hesla jenom pro uživatele z vlastní organizační jednotky, vytvořte skupinu, která bude obsahovat uživatele v této organizační jednotce.
>
>

## <a name="next-steps"></a>Další postup
* [Přečtěte si o službě Active Directory jemně odstupňované zásady hesel.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Konfigurace podrobných zásad pro hesla pomocí centra pro správu služby AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
