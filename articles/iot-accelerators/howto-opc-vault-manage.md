---
title: Jak spravovat službu OPC trezor certifikátů – Azure | Microsoft Docs
description: Správa certifikátů kořenové certifikační autority OPC trezoru a uživatelských oprávnění
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91281911"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Správa služby OPC trezor certifikátů

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek vysvětluje úlohy správy pro službu správy certifikátů trezoru OPC v Azure. Obsahuje informace o tom, jak obnovit certifikáty certifikační autority vystavitele, jak obnovit seznam odvolaných certifikátů (CRL) a jak udělit a odvolat přístup uživatelů.

## <a name="create-or-renew-the-root-ca-certificate"></a>Vytvořit nebo obnovit certifikát kořenové certifikační autority

Po nasazení úložiště OPC je potřeba vytvořit certifikát kořenové certifikační autority. Bez platného certifikátu certifikační autority vystavitele nemůžete podepisovat nebo vystavovat certifikáty aplikací. Informace o správě certifikátů s rozumnými a bezpečnými životnostmi najdete v tématu [certifikáty](howto-opc-vault-secure-ca.md#certificates) . Prodloužit platnost certifikátu certifikační autority vystavitele po polovině své životnosti. Při obnovení Zvažte také, že konfigurovaná doba platnosti nově podepsaného certifikátu aplikace by neměla přesáhnout dobu života certifikátu certifikační autority vystavitele.
> [!IMPORTANT]
> K vytvoření nebo obnovení certifikátu certifikační autority vystavitele je nutná role správce.

1. Otevřete svou službu Certificate Service na adrese `https://myResourceGroup-app.azurewebsites.net` a přihlaste se.
2. Přejít na **skupiny certifikátů**.
3. Je uvedena jedna výchozí skupina certifikátů. Vyberte **Upravit**.
4. V části **Upravit podrobnosti skupiny certifikátů**můžete upravit název subjektu a dobu života certifikační autority a certifikátů aplikací. Předmět a životnost by se měly nastavit jenom jednou, než se vystaví první certifikát certifikační autority. Změny doby života během operací můžou vést k nekonzistentním životnosti vydaných certifikátů a seznamů CRL.
5. Zadejte platný předmět (například `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > Pokud změníte předmět, je nutné obnovit certifikát vystavitele, jinak se služba nepodaří podepsat certifikáty aplikací. Předmět konfigurace se kontroluje v předmětu aktivního certifikátu vystavitele. Pokud se subjekty neshodují, podepsání certifikátu je odmítnuto.
6. Vyberte **Uložit**.
7. Pokud v tomto okamžiku dojde k chybě "zakázáno", přihlašovací údaje uživatele nemají oprávnění správce pro úpravu nebo vytvoření nového kořenového certifikátu. Uživatel, který službu nasadil, má ve výchozím nastavení role správce a podepisování pomocí služby. Ostatní uživatelé musí být přidáni do rolí schvalovatel, Writer nebo Administrators, jak je to vhodné v rámci registrace aplikace Azure Active Directory (Azure AD).
8. Vyberte **Podrobnosti**. Mělo by se zobrazit aktualizované informace.
9. Vyberte **obnovit certifikát certifikační autority** a vydejte prvního certifikátu certifikační autority vystavitele, nebo obnovte certifikát vystavitele. Pak vyberte **OK**.
10. Po několika sekundách se zobrazí **Podrobnosti o certifikátu**. Pokud chcete stáhnout nejnovější certifikát certifikační autority a seznam CRL pro distribuci do vašich aplikací OPC UA, vyberte **vystavitele** nebo **seznam CRL**.

Služba OPC UA Certificate Management je teď připravená k vydávání certifikátů pro aplikace OPC UA.

## <a name="renew-the-crl"></a>Obnovit seznam CRL

Obnovení seznamu odvolaných certifikátů je aktualizace, která by se měla distribuovat do aplikací v pravidelných intervalech. Zařízení OPC UA, která podporují rozšíření x509 distribučního bodu seznamu CRL, můžou přímo aktualizovat seznam odvolaných certifikátů z koncového bodu mikroslužeb. Jiná zařízení OPC UA můžou vyžadovat ruční aktualizace, nebo je můžete aktualizovat pomocí rozšíření nabízených oznámení GDS serveru (*) a aktualizovat seznamy důvěryhodných certifikátů pomocí certifikátů a seznamů CRL.

V následujícím pracovním postupu se všechny žádosti o certifikát v odstraněných stavech odvolají v seznamech CRL, které odpovídají certifikátu certifikační autority vystavitele, pro který byly vystavené. Číslo verze seznamu odvolaných certifikátů se zvyšuje o 1. <br>
> [!NOTE]
> Všechny vydané seznamy CRL platí až do vypršení platnosti certifikátu certifikační autority vystavitele. Je to proto, že specifikace OPC UA nevyžaduje pro seznam CRL povinný a deterministický distribuční model.

> [!IMPORTANT]
> K obnovení seznamu CRL vystavitele je nutná role správce.

1. Otevřete svou službu Certificate Service na adrese `https://myResourceGroup.azurewebsites.net` a přihlaste se.
2. Přejít na stránku **skupiny certifikátů** .
3. Vyberte **Podrobnosti**. Měla by se zobrazit informace o aktuálním certifikátu a seznamu CRL.
4. Vyberte **aktualizovat seznam odvolaných certifikátů (CRL)** , aby se vydával aktualizovaný seznam CRL pro všechny aktivní certifikáty vystavitelů v úložišti OPC trezoru.
5. Po několika sekundách se zobrazí **Podrobnosti o certifikátu**. Pokud chcete stáhnout nejnovější certifikát certifikační autority a seznam CRL pro distribuci do vašich aplikací OPC UA, vyberte **vystavitele** nebo **seznam CRL**.

## <a name="manage-user-roles"></a>Správa rolí uživatele

Role uživatelů pro mikroslužbu trezoru OPC můžete spravovat v podnikové aplikaci Azure AD. Podrobný popis definic rolí najdete v tématu [role](howto-opc-vault-secure-ca.md#roles).

Ve výchozím nastavení se ověřený uživatel v tenantovi může ke službě přihlásit jako čtenář. Role s vyššími oprávněními vyžadují ruční správu v Azure Portal nebo pomocí PowerShellu.

### <a name="add-user"></a>Přidat uživatele

1. Otevřete web Azure Portal.
2. Přejít na **Azure Active Directory**  >  **podnikové aplikace**
3. Vyberte registraci mikroslužby trezoru OPC (ve výchozím nastavení máte `resourceGroupName-service` ).
4. Přejít na **uživatele a skupiny**.
5. Vyberte **Přidat uživatele**.
6. Vyberte nebo Pozvěte uživatele k přiřazení ke konkrétní roli.
7. Vyberte roli pro uživatele.
8. Vyberte **Přiřadit**.
9. Pro uživatele v roli správce nebo schvalovatele pokračujte přidáním zásad přístupu Azure Key Vault.

### <a name="remove-user"></a>Odebrání uživatele

1. Otevřete web Azure Portal.
2. Přejít na **Azure Active Directory**  >  **podnikové aplikace**
3. Vyberte registraci mikroslužby trezoru OPC (ve výchozím nastavení máte `resourceGroupName-service` ).
4. Přejít na **uživatele a skupiny**.
5. Vyberte uživatele s rolí, které chcete odebrat, a pak vyberte **Odebrat**.
6. U odebraných uživatelů v roli správce nebo schvalovatele je také odeberte ze zásad Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Přidat zásady přístupu uživatele do Azure Key Vault

Pro schvalovatele a správce jsou vyžadovány další zásady přístupu.

Ve výchozím nastavení má identita služby pouze omezená oprávnění pro přístup k Key Vault, aby se předešlo operacím se zvýšenými oprávněními nebo změnám bez zosobnění uživatele. Základní oprávnění služby jsou Get a list pro tajné klíče a certifikáty. U tajných klíčů existuje jenom jedna výjimka: služba může privátní klíč odstranit z úložiště tajného kódu, až ho přijme uživatel. Všechny ostatní operace vyžadují oprávnění zosobněná uživatelem.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Pro roli schvalovatele je nutné do Key Vault přidat následující oprávnění.

1. Otevřete web Azure Portal.
2. Umožňuje přejít do trezoru OPC `resourceGroupName` , který se používá během nasazování.
3. Přejít na Key Vault `resourceGroupName-xxxxx` .
4. Přejděte na **zásady přístupu**.
5. Vyberte **Přidat nový**.
6. Přeskočit šablonu. Neexistuje žádná šablona, která by odpovídala požadavkům.
7. Zvolte **Vybrat objekt zabezpečení**a vyberte uživatele, který chcete přidat, nebo Pozvěte nového uživatele k tenantovi.
8. Vyberte následující **oprávnění klíče**: **získat**, **vypsat**a **podepsat**.
9. Vyberte následující **oprávnění tajných klíčů**: **získat**, **vypsat**, **nastavit**a **Odstranit**.
10. Vyberte následující **oprávnění certifikátu**: **načíst** a **seznam**.
11. Vyberte **OK**a vyberte **Uložit**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Pro roli správce musí být do Key Vault přidána následující oprávnění.

1. Otevřete web Azure Portal.
2. Umožňuje přejít do trezoru OPC `resourceGroupName` , který se používá během nasazování.
3. Přejít na Key Vault `resourceGroupName-xxxxx` .
4. Přejděte na **zásady přístupu**.
5. Vyberte **Přidat nový**.
6. Přeskočit šablonu. Neexistuje žádná šablona, která by odpovídala požadavkům.
7. Zvolte **Vybrat objekt zabezpečení**a vyberte uživatele, který chcete přidat, nebo Pozvěte nového uživatele k tenantovi.
8. Vyberte následující **oprávnění klíče**: **získat**, **vypsat**a **podepsat**.
9. Vyberte následující **oprávnění tajných klíčů**: **získat**, **vypsat**, **nastavit**a **Odstranit**.
10. Vyberte následující **oprávnění certifikátu**: **získat**, **vypsat**, **aktualizovat**, **vytvořit**a **importovat**.
11. Vyberte **OK**a vyberte **Uložit**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Odebrat zásady přístupu uživatele z Azure Key Vault

1. Otevřete web Azure Portal.
2. Umožňuje přejít do trezoru OPC `resourceGroupName` , který se používá během nasazování.
3. Přejít na Key Vault `resourceGroupName-xxxxx` .
4. Přejděte na **zásady přístupu**.
5. Vyhledejte uživatele, který chcete odebrat, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat certifikáty a uživatele trezoru OPC, můžete:

> [!div class="nextstepaction"]
> [Zabezpečená komunikace zařízení OPC](howto-opc-vault-secure.md)
