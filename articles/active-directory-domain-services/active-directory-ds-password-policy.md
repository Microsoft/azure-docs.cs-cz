---
title: 'Azure Active Directory Domain Services: Zásady hesel | Dokumentace Microsoftu'
description: Vysvětlení zásad hesel na spravované domény
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: maheshu
ms.openlocfilehash: 66db13ea2b40d32b03d57e7d039863315312d734
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043103"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Zásady uzamčení hesla a účtu na spravované domény
Tento článek vysvětluje výchozí zásady hesel ve spravované doméně. Věnuje se také, jak můžete nakonfigurovat tyto zásady.

## <a name="fine-grained-password-policies-fgpp"></a>Zásady pro hesla jemné jemněji (podrobných zásad pro HESLA)
Pomocí podrobné zásady pro hesla můžete určit řadu zásady pro hesla v jedné doméně. Podrobné zásady pro HESLA můžete použít různá omezení zásady uzamčení hesla a účtu pro různé skupiny uživatelů v doméně. Například můžete použít nastavení striktní hesla k privilegovaným účtům.

Můžete nakonfigurovat následující nastavení hesla pomocí podrobných zásad pro HESLA:
* Minimální délka hesla
* Historii hesel
* Heslo musí splňovat požadavky na složitost
* Minimální stáří hesla
* Maximální stáří hesla
* Zásady uzamčení účtů
    * Doba uzamčení účtu
    * Počet neúspěšných pokusů o přihlášení povoleno
    * Resetování neúspěšných pokusů o přihlášení počet po


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Výchozí nastavení zásad hesel podrobné ve spravované doméně
Následující snímek obrazovky ukazuje výchozí jemné podrobné zásady hesel nakonfigurované ve spravované doméně Azure AD Domain Services.

![Výchozí podrobné zásady hesel](./media/how-to/default-fgpp.png)

> [!NOTE]
> Nemůžete upravovat ani odstranit výchozí integrovanou podrobné zásady hesel. Členové skupiny 'Správci AAD DC' můžete vytvořit vlastní podrobných zásad pro HESLA a nakonfigurovat ho chcete přepsat (přednost) výchozí integrovanou podrobných zásad pro HESLA.
>
>

## <a name="password-policy-settings"></a>Nastavení zásad pro hesla
Ve spravované doméně jsou ve výchozím nastavení nakonfigurované následující zásady pro hesla:
* Minimální délka hesla (ve znacích): 7
* Maximální stáří hesla (životnost): 90 dnů
* Heslo musí splňovat požadavky na složitost

### <a name="account-lockout-settings"></a>Nastavení uzamčení účtu
Ve spravované doméně jsou ve výchozím nastavení nakonfigurované následující zásady uzamčení účtu:
* Doba uzamčení účtu: 30
* Počet neúspěšných pokusů o přihlášení povolený: 5
* Resetování neúspěšných pokusů o přihlášení počet po: 30 minut

Efektivní uživatelské účty jsou uzamčen po dobu 30 minut-li pět neplatných zadání hesla se používají během 2 minut. Účty se automaticky odemknout po 30 minutách.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Vytvoření vlastního hesla podrobné zásady (podrobných zásad pro HESLA) ve spravované doméně
Můžete vytvořit vlastní podrobných zásad pro HESLA a použít ho na konkrétní skupiny ve vaší spravované doméně. Tato konfigurace ve výsledku přepíše výchozí podrobných zásad pro HESLA nakonfigurované pro spravovanou doménu.

> [!TIP]
> Pouze členové **"Správci AAD DC"** skupiny mají oprávnění k vytvoření vlastní zásady pro hesla podrobné.
>
>

Kromě toho můžete vytvořit vlastní heslo jemné jemněji zásady a použít je pro všechny vlastní organizační jednotky, vytvořte ve spravované doméně.

Vlastní podrobných zásad pro HESLA můžete nakonfigurovat z následujících důvodů:
* Chcete-li nastavit zásady uzamčení jiný účet.
* Konfigurovat výchozí nastavení doby platnosti hesla pro spravovanou doménu.

Pokud chcete vytvořit vlastní podrobných zásad pro HESLA ve vaší spravované doméně:
1. Přihlaste se k virtuálnímu počítači Windows můžete použít ke správě vaší spravované domény. Pokud ho nemáte, postupujte podle pokynů k [Správa spravované domény](active-directory-ds-admin-guide-administer-domain.md)
2. Spusťte **Centrum správy služby Active Directory** na virtuálním počítači.
3. Klikněte na název domény (například "contoso100.com").
4. Dvakrát klikněte na panel **systému** otevřete kontejneru systému.
5. Dvakrát klikněte na panel **kontejner nastavení hesel**.
6. Zobrazí se výchozí názvem integrované podrobných zásad pro HESLA pro spravovanou doménu **AADDSSTFPSO**. Nelze upravit toto integrované podrobných zásad pro HESLA. Můžete však vytvořit nové vlastní potlačení podrobných zásad pro HESLA výchozí podrobných zásad pro HESLA.
7. Na **úlohy** panelu klikněte pravým tlačítkem, **nový** a klikněte na tlačítko **nastavení hesla**.
8. V **vytvořit nastavení hesla** dialogové okno, zadejte nastavení vlastní heslo použít jako součást vlastní podrobných zásad pro HESLA. Nezapomeňte si odpovídajícím způsobem nastavit prioritu přepsat výchozí nastavení podrobných zásad pro HESLA.

  ![Vytvořit vlastní podrobných zásad pro HESLA](./media/how-to/custom-fgpp.png)

  > [!TIP]
  > **Nezapomeňte zrušit zaškrtnutí chránit před náhodným odstraněním možnost.** Pokud je vybraná tato možnost, nelze uložit podrobných zásad pro HESLA.
  >
  >

9. V **vztahuje se přímo na**, klikněte na tlačítko **přidat** tlačítko. V **vybrat uživatele nebo skupiny** dialogového okna, klikněte na tlačítko **umístění** tlačítko.

  ![Vyberte uživatele a skupiny](./media/how-to/fgpp-applies-to.png)

10. V **umístění** dialogového okna, rozbalte název domény a klikněte na tlačítko **uživatelé AADDC**. Nyní můžete vybrat skupinu z předdefinovaných uživatele organizační jednotky, pro kterou chcete použít podrobných zásad pro HESLA.

  ![Vybrat organizační jednotky této skupiny patří do](./media/how-to/fgpp-container.png)

11. Zadejte název skupiny a klikněte na tlačítko **Kontrola názvů** tlačítko Ověřit skupině existuje.

  ![Vyberte skupinu, kterou chcete použít podrobné zásady pro HESLA](./media/how-to/fgpp-apply-group.png)

12. Název skupiny se zobrazí v **vztahuje se přímo na** oddílu. Klikněte na tlačítko **OK** tlačítka tyto změny uložit.

  ![Podrobné zásady pro HESLA použít](./media/how-to/fgpp-applied.png)

> [!TIP]
> **K uplatnění zásad vlastního hesla pro uživatelské účty ve vlastní organizační jednotce:** zásady pro hesla jemné jemněji lze použít pouze u skupin. Konfigurace zásad vlastní heslo jenom pro uživatele z vlastní organizační jednotky, vytvořte skupinu, která obsahuje uživatele v dané organizační jednotce.
>
>

## <a name="next-steps"></a>Další postup
* [Další informace o zásadách jemné jemněji hesla služby Active Directory](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394)
* [Konfigurace zásad podrobné hesla pomocí centra pro správu AD](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
