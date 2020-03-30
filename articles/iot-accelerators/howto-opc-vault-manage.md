---
title: Správa certifikační služby OPC Vault – Azure | Dokumenty společnosti Microsoft
description: Spravujte kořenové certifikáty certifikační autority OPC Vault a uživatelská oprávnění.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203651"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Správa certifikační služby OPC Vault

Tento článek vysvětluje úkoly správy pro službu správy certifikátů OPC Vault v Azure. Obsahuje informace o tom, jak obnovit certifikáty certifikační autority vystavitho na certifikáty, jak obnovit seznam odvolaných certifikátů (CRL) a jak udělit a odvolat přístup uživatelů.

## <a name="create-or-renew-the-root-ca-certificate"></a>Vytvoření nebo obnovení kořenového certifikátu certifikační autority

Po nasazení opc trezoru je nutné vytvořit kořenový certifikát certifikační autority. Bez platného certifikátu certifikační autority vystavitvystavittele nelze podepsat ani vydat certifikáty aplikace. Odkazovat na [certifikáty](howto-opc-vault-secure-ca.md#certificates) pro správu certifikátů s přiměřenou, bezpečné životnosti. Po polovině jeho životnosti obnovte certifikát certifikační autority vystavittele. Při obnovování také zvažte, že nakonfigurovaná doba života nově podepsaného certifikátu aplikace by neměla přesáhnout dobu platnosti certifikátu certifikační autority vystavittele.
> [!IMPORTANT]
> K vytvoření nebo obnovení certifikátu certifikační autority vystavittele je vyžadována role Správce.

1. Otevřete certifikační `https://myResourceGroup-app.azurewebsites.net`službu na vysmívejte a přihlaste se.
2. Přejděte na **skupiny certifikátů**.
3. Je uvedena jedna výchozí skupina certifikátů. Vyberte **Upravit**.
4. V **části Upravit podrobnosti skupiny certifikátů**můžete upravit název subjektu a životnost certifikačníautority a certifikátů aplikace. Předmět a životnost by měly být nastaveny pouze jednou před vydáním prvního certifikátu certifikační autority. Životnost změny během operací může mít za následek nekonzistentní životnost vydané certifikáty a seznamy crl.
5. Zadejte platný předmět `CN=My CA Root, O=MyCompany, OU=MyDepartment`(například).<br>
   > [!IMPORTANT]
   > Pokud změníte předmět, je nutné obnovit certifikát vystavittele, jinak se službě nepodaří podepsat certifikáty aplikace. Předmět konfigurace je zkontrolován podle předmětu aktivního certifikátu vystavittele. Pokud se předměty neshodují, podpis certifikátu je odmítnut.
6. Vyberte **Uložit**.
7. Pokud v tomto okamžiku narazíte na "zakázanou" chybu, pověření uživatele nemají oprávnění správce k úpravě nebo vytvoření nového kořenového certifikátu. Ve výchozím nastavení má uživatel, který službu nasadil, s rolí správce a podepisování se službou. Ostatní uživatelé je třeba přidat do role schvalovatel, zapisovatel nebo správce, podle potřeby v registraci aplikace Azure Active Directory (Azure AD).
8. Vyberte **Podrobnosti**. To by mělo zobrazit aktualizované informace.
9. Výběrem **možnosti Obnovit certifikát certifikační autority** vystavíte první certifikát certifikační autority vystavitena nebo certifikát vystavittele. Pak vyberte **OK**.
10. Po několika sekundách se zobrazí **podrobnosti o certifikátu**. Chcete-li stáhnout nejnovější certifikát certifikační autority a seznam CRL pro distribuci do aplikací OPC UA, vyberte **možnost Vystavitnebo** **Crl**.

Nyní je služba správy certifikátů OPC UA připravena vydávat certifikáty pro aplikace OPC UA.

## <a name="renew-the-crl"></a>Obnovení seznamu odvolaná kniha

Obnovení seznamu odvolaných dat je aktualizace, která by měla být distribuována do aplikací v pravidelných intervalech. Zařízení OPC UA, která podporují rozšíření crl distribučního bodu X509, mohou přímo aktualizovat seznam CRL z koncového bodu mikroslužeb. Jiná zařízení OPC UA mohou vyžadovat ruční aktualizace nebo mohou být aktualizována pomocí nabízených rozšíření serveru GDS (*) k aktualizaci seznamů důvěryhodnosti pomocí certifikátů a seznamů crl.

V následujícím pracovním postupu jsou všechny žádosti o certifikát y v odstraněných stavech odvolány v seznamech CRL, které odpovídají certifikátu certifikační autority vystavitho služby, pro který byly vydány. Číslo verze seznamu odvolaných odvolaných čísel je se zpřísňují o 1. <br>
> [!NOTE]
> Všechny vydané seznamy CRL jsou platné až do vypršení platnosti certifikátu certifikační autority vystavitholistu. Důvodem je, že specifikace OPC UA nevyžaduje povinný deterministický distribuční model pro CRL.

> [!IMPORTANT]
> K obnovení seznamu CRL vystavittele je vyžadována role správce.

1. Otevřete certifikační `https://myResourceGroup.azurewebsites.net`službu na vysmívejte a přihlaste se.
2. Přejděte na stránku **Skupiny certifikátů.**
3. Vyberte **Podrobnosti**. To by mělo zobrazit aktuální certifikát a informace o seznamu odvolaných certifikátů.
4. Vyberte **Aktualizovat seznam odvolaných certifikátů (CRL)** a vystavte aktualizovaný seznam ODVol a v úložišti OPC Vault vyberte aktualizovaný seznam ODVol pro všechny aktivní certifikáty vystavitetele.
5. Po několika sekundách se zobrazí **podrobnosti o certifikátu**. Chcete-li stáhnout nejnovější certifikát certifikační autority a seznam CRL pro distribuci do aplikací OPC UA, vyberte **možnost Vystavitnebo** **Crl**.

## <a name="manage-user-roles"></a>Správa rolí uživatele

Můžete spravovat role uživatelů pro mikroslužbu OPC Vault v aplikaci Azure AD Enterprise. Podrobný popis definic rolí naleznete v tématu [Role](howto-opc-vault-secure-ca.md#roles).

Ve výchozím nastavení se ověřený uživatel v tenantovi může přihlásit ke službě jako čtenář. Vyšší privilegované role vyžadují ruční správu na webu Azure Portal nebo pomocí PowerShellu.

### <a name="add-user"></a>Přidání uživatele

1. Otevřete web Azure Portal.
2. Přejděte na aplikace **Azure Active Directory** > **Enterprise**.
3. Zvolte registraci mikroslužby OPC Vault `resourceGroupName-service`(ve výchozím nastavení vaše).
4. Přejděte na **uživatelé a skupiny**.
5. Vyberte **Přidat uživatele**.
6. Vyberte nebo pozvěte uživatele k přiřazení k určité roli.
7. Vyberte roli pro uživatele.
8. Vyberte **Přiřadit**.
9. Pro uživatele v roli správce nebo schvalovatele pokračujte v přidávání zásad přístupu k úložišti klíčů Azure.

### <a name="remove-user"></a>Odebrání uživatele

1. Otevřete web Azure Portal.
2. Přejděte na aplikace **Azure Active Directory** > **Enterprise**.
3. Zvolte registraci mikroslužby OPC Vault `resourceGroupName-service`(ve výchozím nastavení vaše).
4. Přejděte na **uživatelé a skupiny**.
5. Vyberte uživatele s rolí, která chcete odebrat, a pak vyberte **Odebrat**.
6. Pro odebrané uživatele v roli správce nebo schvalovatel je také odeberte ze zásad Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Přidání zásad přístupu uživatelů do služby Azure Key Vault

Pro schvalovatele a správce jsou vyžadovány další zásady přístupu.

Ve výchozím nastavení má identita služby pouze omezená oprávnění pro přístup k trezoru klíčů, aby se zabránilo zvýšeným operacím nebo změnám bez zosobnění uživatele. Základní oprávnění služby jsou Získat a Seznam, pro tajné klíče a certifikáty. Pro tajné klíče existuje pouze jedna výjimka: služba může odstranit soukromý klíč z tajného úložiště poté, co je přijat uživatelem. Všechny ostatní operace vyžadují oprávnění zosobněného uživatelem.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Pro roli schvalovatele musí být do trezoru klíčů přidána následující oprávnění.

1. Otevřete web Azure Portal.
2. Přejděte do trezoru `resourceGroupName`OPC , který se používá během nasazení.
3. Přejděte do `resourceGroupName-xxxxx`trezoru klíčů .
4. Přejděte na **zásady přístupu**.
5. Vyberte **Přidat nový**.
6. Přeskočte šablonu. Neexistuje žádná šablona, která odpovídá požadavkům.
7. Zvolte **Vybrat hlavní objekt**a vyberte uživatele, který má být přidán, nebo pozvat nového uživatele do tenanta.
8. Vyberte následující **klíčová oprávnění**: **Získat**, **Seznam**a **Podepsat**.
9. Vyberte následující **tajná oprávnění**: **Získat**, **Seznam**, **Nastavit**a **Odstranit**.
10. Vyberte následující **oprávnění certifikátu**: **Získat** a **vypsat**.
11. Vyberte **OK**a vyberte **Uložit**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Pro roli správce musí být do trezoru klíčů přidána následující oprávnění.

1. Otevřete web Azure Portal.
2. Přejděte do trezoru `resourceGroupName`OPC , který se používá během nasazení.
3. Přejděte do `resourceGroupName-xxxxx`trezoru klíčů .
4. Přejděte na **zásady přístupu**.
5. Vyberte **Přidat nový**.
6. Přeskočte šablonu. Neexistuje žádná šablona, která odpovídá požadavkům.
7. Zvolte **Vybrat hlavní objekt**a vyberte uživatele, který má být přidán, nebo pozvat nového uživatele do tenanta.
8. Vyberte následující **klíčová oprávnění**: **Získat**, **Seznam**a **Podepsat**.
9. Vyberte následující **tajná oprávnění**: **Získat**, **Seznam**, **Nastavit**a **Odstranit**.
10. Vyberte následující **oprávnění certifikátu**: **Získat**, **Seznam**, **Aktualizovat**, **Vytvořit**a **Importovat**.
11. Vyberte **OK**a vyberte **Uložit**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Odebrání zásad přístupu uživatelů z trezoru klíčů Azure

1. Otevřete web Azure Portal.
2. Přejděte do trezoru `resourceGroupName`OPC , který se používá během nasazení.
3. Přejděte do `resourceGroupName-xxxxx`trezoru klíčů .
4. Přejděte na **zásady přístupu**.
5. Najděte uživatele, který má být odstraněn, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili spravovat certifikáty OPC Vault a uživatele, můžete:

> [!div class="nextstepaction"]
> [Bezpečná komunikace OPC zařízení](howto-opc-vault-secure.md)
