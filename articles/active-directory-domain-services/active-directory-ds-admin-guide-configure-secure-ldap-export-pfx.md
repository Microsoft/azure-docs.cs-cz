---
title: Vytvoření certifikátem protokolu secure LDAP pro správu domény Azure AD Domain Services | Dokumentace Microsoftu
description: Vytvoření certifikátem protokolu secure LDAP pro Azure AD Domain Services spravovat doménu
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: maheshu
ms.openlocfilehash: 0bccb90029f319805086c12162f4e0eec4939865
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430072"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Vytvoření. Soubor PFX s certifikátem zabezpečeného protokolu LDAP (LDAPS) pro spravovanou doménu

## <a name="before-you-begin"></a>Než začnete
Kompletní [úkol 1: získání certifikátu pro protokol secure LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Úloha 2: Export certifikát protokolu secure LDAP pro. Soubor PFX
Předtím, než se pustíte do této úlohy, získat certifikát protokolu secure LDAP od veřejné certifikační autority nebo vytvořit certifikát podepsaný svým držitelem.

Export certifikátu protokolu LDAPS pro. Soubor PFX:

1. Stisknutím klávesy **Start** tlačítko a typ **R**. V **spustit** dialogové okno, zadejte **konzoly mmc** a klikněte na tlačítko **OK**.

    ![Spustit konzolu MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Na **řízení uživatelských účtů** výzva, klikněte na tlačítko **Ano** chcete spustit konzolu MMC (Microsoft Management Console) jako správce.
3. Z **souboru** nabídky, klikněte na tlačítko **Přidat/odebrat modul Snap-in...** .

    ![Přidat modul snap-in konzoly MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. V **přidat nebo odebrat moduly Snap in** dialogového okna, vyberte **certifikáty** modul snap-in a klikněte **Přidat >** tlačítko.

    ![Přidat modul snap-in Certifikáty konzoly MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. V **modul snap-in Certifikáty** průvodce, vyberte **účet počítače** a klikněte na tlačítko **Další**.

    ![Přidat modul snap-in Certifikáty pro účet počítače.](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Na **vybrat počítač** stránce **místní počítač: (počítač je spuštěna tato konzola)** a klikněte na tlačítko **Dokončit**.

    ![Přidat modul snap-in Certifikáty - vybrat počítače](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. V **přidat nebo odebrat moduly Snap in** dialogového okna, klikněte na tlačítko **OK** přidat modul snap-in Certifikáty konzoly MMC.

    ![Přidat modul snap-in Certifikáty konzoly MMC – dokončení](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. V okně konzoly MMC kliknutím rozbalte **kořenový adresář konzoly**. Měli byste vidět modulu snap-in Certifikáty načíst. Klikněte na tlačítko **certifikáty (místní počítač)** rozbalte. Rozbalte kliknutím **osobní** uzel, za nímž následuje **certifikáty** uzlu.

    ![Úložiště Open osobní certifikáty](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Měli byste vidět certifikát podepsaný svým držitelem, který jsme vytvořili. Můžete prozkoumat vlastnosti certifikátu pro ověření, že kryptografický otisk se shoduje s hlášené ve windows Powershellu, když jste vytvořili certifikát.
10. Vyberte certifikát podepsaný svým držitelem a **klikněte pravým tlačítkem myši**. V místní nabídce vyberte **všechny úkoly** a vyberte **exportu...** .

    ![Export certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. V **Průvodce exportem certifikátu**, klikněte na tlačítko **Další**.

    ![Průvodce exportem certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Na **exportovat soukromý klíč** stránce **Ano, exportovat soukromý klíč**a klikněte na tlačítko **Další**.

    ![Exportovat privátní klíč certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Je nutné exportovat privátní klíč spolu se certifikát. Pokud zadáte PFX, který neobsahuje privátní klíč pro certifikát, povolování protokolu secure LDAP pro spravovanou doménu nezdaří.
    >
    >

13. Na **formát souboru pro Export** stránce **Personal Information Exchange – PKCS #12 (. PFX)** jako formát souboru pro exportovaný certifikát.

    ![Formát souboru pro export certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Pouze. Formát souboru PFX je podporován. Neexportovat certifikát, který. Formát souboru CER.
    >
    >

14. Na **zabezpečení** stránky, vyberte **heslo** možnost a zadejte heslo k ochraně. Soubor PFX. Toto heslo si zapamatujte, protože budete ji potřebovat v dalším úkolem. Klikněte na **Další**.

    ![Heslo pro export certifikátu ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Toto heslo si poznamenejte. Je třeba při povolování protokolu secure LDAP pro tuto spravovanou doménu v [úloha 3 – povolení protokolu LDAPS pro spravované domény](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. Na **soubor pro Export** stránky, zadejte název souboru a umístění, ve kterém chcete exportovat certifikát.

    ![Cesta pro export certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Klikněte na následující stránce **Dokončit** exportujte certifikát do souboru PFX. Pokud certifikát byl exportován, měli byste vidět potvrzovací dialogové okno.

    ![Export certifikátu Hotovo](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Další krok
[Úloha 3: povolení protokolu LDAPS pro spravované domény](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
