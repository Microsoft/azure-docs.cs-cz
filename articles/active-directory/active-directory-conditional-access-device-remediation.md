---
title: Odsud se tam nelze dostat na webu Azure Portal ze zařízení s Windows | Dokumentace Microsoftu
description: Dozvíte se, odkud pochází zpráva, že odsud se tam nelze dostat, a co můžete zkontrolovat, aby se vám toto dialogové okno nezobrazovalo.
services: active-directory
keywords: přístup podmíněný zařízením, registrace zařízení, povolení registrace zařízení, registrace zařízení a MDM
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 5ad9b01d3821b481fe3255c821e8674dcb26b322
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2018
---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Odsud se tam nelze dostat pro zařízení s Windows

Při pokusu například o přístup k intranetu Sharepointu Online ve vaší organizaci můžete narazit na stránku, která uvádí, že *odsud se tam nelze dostat*. Tato stránka se zobrazuje, protože správce nakonfiguroval zásady podmíněného přístupu, které za určitých podmínek brání v přístupu k prostředkům vaší organizace. Možná bude nutné se při řešení těchto potíží obrátit na helpdesk nebo správce, ale existuje několik věcí, které můžete nejdřív vyzkoušet sami.

Pokud používáte zařízení s **Windows**, měli byste zkontrolovat toto:

- Používáte podporovaný prohlížeč?

- Používáte ve vašem zařízení podporovanou verzi systému Windows?

- Odpovídá vaše zařízení požadavkům?






## <a name="supported-browser"></a>Podporovaný prohlížeč

Pokud váš správce nakonfiguroval zásady podmíněného přístupu, můžete pro přístup k prostředkům vaší organizace používat jenom podporovaný prohlížeč. V zařízeních se systémem Windows se podporují jenom prohlížeče **Internet Explorer** a **Microsoft Edge**.

To, jestli k prostředku nejde přistupovat kvůli nepodporovanému prohlížeči, snadno zjistíte v sekci podrobností chybové stránky:

![Zpráva „Odsud se tam nelze dostat“ pro nepodporované prohlížeče](./media/active-directory-conditional-access-device-remediation/02.png "Scénář")

Jedinou možností odstranění problému je použít prohlížeč, který aplikace pro platformu vašeho zařízení podporuje. Úplný seznam podporovaných prohlížečů najdete v části [Podporované prohlížeče](active-directory-conditional-access-supported-apps.md).  


## <a name="supported-versions-of-windows"></a>Podporované verze Windows

Pro operační systém Windows v zařízení musí být splněné tyto podmínky: 

- Pokud v zařízení používáte desktopový operační systém Windows, musí to být systém Windows 7 nebo novější.
- Pokud v zařízení používáte serverový operační systém Windows, musí to být systém Windows Server 2008 R2 nebo novější. 


## <a name="compliant-device"></a>Odpovídající zařízení

Je možné, že správce nakonfiguroval zásady podmíněného přístupu, které umožňují přístup k prostředkům vaší organizace jenom z odpovídajících zařízení. Zařízení je odpovídající, pokud je připojené k místní službě Active Directory nebo ke službě Azure Active Directory.

To, jestli k prostředku nejde přistupovat kvůli zařízení, které neodpovídá požadavkům, snadno zjistíte v sekci podrobností chybové stránky:
 
![Zprávy „Odsud se tam nelze dostat“ pro neregistrovaná zařízení](./media/active-directory-conditional-access-device-remediation/01.png "Scénář")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>Je zařízení připojené k místní službě Active Directory?

**Pokud je zařízení připojené k místní službě Active Directory ve vaší organizaci:**

1. Přihlaste se k Windows pomocí svého pracovního účtu (účtu služby Active Directory).
2. Připojte se k podnikové síti prostřednictvím virtuální privátní sítě (VPN) nebo technologie DirectAccess.
3. Jakmile budete připojeni, uzamkněte relaci Windows stisknutím kláves Windows + L.
4. Odemkněte relaci Windows zadáním přihlašovacích údajů ke svému pracovnímu účtu.
5. Počkejte zhruba minutu a znovu se pokuste o přístup k aplikaci nebo službě.
6. Pokud se zobrazí stejná stránka, obraťte se na svého správce a sdělte mu podrobnosti, které získáte po kliknutí na odkaz **Podrobnosti**.


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>Zařízení není připojené k místní službě Active Directory?

Pokud zařízení není připojené k místní službě Active Directory a používá systém Windows 10, máte dvě možnosti:

* Spusťte službu Azure AD Join
* Přidejte svůj pracovní nebo školní účet do Windows.

Informace o rozdílech mezi těmito dvěma možnostmi najdete v článku [Používání zařízení s Windows 10 na pracovišti](active-directory-azureadjoin-windows10-devices.md).  
Pokud zařízení:

- Patří vaší organizaci, měli byste měli spustit Azure AD Join.
- Je osobním zařízením nebo je to Windows Phone, měli byste do Windows přidat svůj pracovní nebo školní účet. 



#### <a name="azure-ad-join-on-windows-10"></a>Azure AD Join v systému Windows 10

Kroky pro připojení zařízení k Azure AD závisejí na verzi Windows 10, kterou na tomto zařízení spouštíte. Pokud chcete zjistit verzi operačního systému Windows 10, spusťte příkaz **winver**: 

![Verze systému Windows](./media/active-directory-conditional-access-device-remediation/03.png )


**Windows 10 Anniversary Update (verze 1607):**

1. Otevřete aplikaci **Nastavení**.
2. Klikněte na **Účty**  >  **Přístup do práce nebo do školy**.
3. Klikněte na **Připojit**.
4. Klikněte na **Připojit toto zařízení k Azure AD**.
5. Ověřte se u své organizace, v případě potřeby proveďte vícefaktorové ověřování a pak postupujte podle zobrazených pokynů.
6. Odhlaste se a znovu se přihlaste pomocí svého pracovního účtu.
7. Znovu se pokuste o přístup k aplikaci.

**Windows 10 November 2015 Update (verze 1511):**

1. Otevřete aplikaci **Nastavení**.
2. Klikněte na **Systém**  >  **O systému**.
3. Klikněte na **Připojit se k Azure AD**.
4. Ověřte se u své organizace, v případě potřeby proveďte vícefaktorové ověřování a pak postupujte podle zobrazených pokynů.
5. Odhlaste se a znovu se přihlaste pomocí svého pracovního účtu (účtu Azure AD).
6. Znovu se pokuste o přístup k aplikaci.


#### <a name="workplace-join-on-windows-81"></a>Připojení k pracovišti ve Windows 8.1

Pokud zařízení není připojené k doméně a běží na systému Windows 8.1, můžete pomocí následujících kroků provést Workplace Join (připojení k pracovišti) a zaregistrovat se do Microsoft Intune:

1. Otevřete **Nastavení počítače**.
2. Klikněte na **Síť**  >  **Pracoviště**.
3. Klikněte na **Připojit**.
4. Ověřte se u své organizace, v případě potřeby proveďte vícefaktorové ověřování a pak postupujte podle zobrazených pokynů.
5. Klikněte na **Zapnout**.
6. Znovu se pokuste o přístup k aplikaci.



#### <a name="add-your-work-or-school-account-to-windows"></a>Přidejte svůj pracovní nebo školní účet do Windows. 


**Windows 10 Anniversary Update (verze 1607):**

1. Otevřete aplikaci **Nastavení**.
2. Klikněte na **Účty**  >  **Přístup do práce nebo do školy**.
3. Klikněte na **Připojit**.
4. Ověřte se u své organizace, v případě potřeby proveďte vícefaktorové ověřování a pak postupujte podle zobrazených pokynů.
5. Znovu se pokuste o přístup k aplikaci.


**Windows 10 November 2015 Update (verze 1511):**

1. Otevřete aplikaci **Nastavení**.
2. Klikněte na **Účty**  >  **Vaše účty**.
3. Klikněte na **Přidat pracovní nebo školní účet**.
4. Ověřte se u své organizace, v případě potřeby proveďte vícefaktorové ověřování a pak postupujte podle zobrazených pokynů.
5. Znovu se pokuste o přístup k aplikaci.





## <a name="next-steps"></a>Další kroky
[Podmíněný přístup ke službě Azure Active Directory](active-directory-conditional-access-azure-portal.md)

