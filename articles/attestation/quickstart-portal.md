---
title: Nastavení ověření Azure pomocí Azure Portal
description: Jak nastavit a nakonfigurovat poskytovatele ověření identity pomocí Azure Portal.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 52c0dd6825c8576d97e10d3f0568ca5fb81e789e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210815"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Rychlý Start: nastavení ověření Azure pomocí Azure Portal

Pomocí následujících kroků můžete spravovat poskytovatele ověření identity pomocí Azure Portal.

## <a name="attestation-provider"></a>Zprostředkovatel ověření identity

### <a name="create-an-attestation-provider"></a>Vytvoření poskytovatele ověření identity

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>Konfigurace poskytovatele pomocí nepodepsaných zásad

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **vytvořit prostředek** .
2.  Do vyhledávacího pole zadejte **ověření identity** .
3.  V seznamu výsledků vyberte **Microsoft Azure ověření identity** .
4.  Na stránce Microsoft Azure ověření identity vyberte **vytvořit** .
5.  Na stránce vytvořit poskytovatele ověření identity zadejte následující vstupy:
    
    **Předplatné**: vyberte předplatné.
    
    **Skupina prostředků**: Vyberte existující skupinu prostředků nebo zvolte **vytvořit nové** a zadejte název skupiny prostředků.
    
    **Název**: je vyžadován jedinečný název.

    **Umístění**: vyberte umístění. 
    
    **Soubor certifikátů podepsaných zásadou**: Nenahrávat soubor certifikátů podepisování zásad ke konfiguraci poskytovatele pomocí nepodepsaných zásad 
6.  Po zadání požadovaných vstupů klikněte na tlačítko **zkontrolovat + vytvořit** .
7.  Opravte problémy s ověřováním, pokud nějaké existují, a klikněte na **vytvořit**.

#### <a name="to-configure-the-provider-with-signed-policies"></a>Postup konfigurace zprostředkovatele s podepsanými zásadami

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **vytvořit prostředek** .
2.  Do vyhledávacího pole zadejte **ověření identity** .
3.  V seznamu výsledků vyberte **Microsoft Azure ověření identity** .
4.  Na stránce Microsoft Azure ověření identity vyberte **vytvořit** .
5.  Na stránce vytvořit poskytovatele ověření identity zadejte následující informace:
    
    a. **Předplatné**: vyberte předplatné.
    
    b. **Skupina prostředků**: Vyberte existující skupinu prostředků nebo zvolte **vytvořit nové** a zadejte název skupiny prostředků.
    
    c. **Název**: je vyžadován jedinečný název.

    d. **Umístění**: vyberte umístění. 
    
    e. **Soubor certifikátů podepsaných zásadou**: Pokud chcete nakonfigurovat poskytovatele ověření identity, který má podpisové certifikáty zásad, nahrajte soubor s certifikáty. [Tady](/azure/attestation/policy-signer-examples) najdete příklady. 
6.  Po zadání požadovaných vstupů klikněte na tlačítko **zkontrolovat + vytvořit** .
7.  Opravte problémy s ověřováním, pokud nějaké existují, a klikněte na **vytvořit**.

### <a name="view-attestation-provider"></a>Zobrazit poskytovatele ověření identity

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity a vyberte ho.

### <a name="delete-attestation-provider"></a>Odstranit poskytovatele ověření identity

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Zaškrtněte políčko a klikněte na **Odstranit** .
4.  Zadejte Ano a klikněte na **Odstranit** [nebo].
1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V horní nabídce klikněte na **Odstranit** a potom klikněte na **Ano** .


## <a name="attestation-policy-signers"></a>Podepsané zásady ověření identity

### <a name="view-policy-signer-certificates"></a>Zobrazit certifikáty podepsané zásadou

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **certifikáty podepisujících zásad** .
5.  Klikněte na **Stáhnout certifikáty zásad podepisování** (tlačítko se zakáže pro poskytovatele ověření identity vytvořené bez požadavku na podepsání zásady).
6.  Stažený textový soubor bude mít všechny certifikáty ve formátu JWS.
a.  Ověřte počet certifikátů a certifikáty, které se stáhly.

### <a name="add-policy-signer-certificate"></a>Přidat certifikát podepsané zásady

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **certifikáty podepisujících zásad** .
5.  V horní nabídce klikněte na tlačítko **Přidat** (tlačítko bude zakázáno pro poskytovatele ověření identity vytvořené bez požadavku na podepsání zásady).
6.  Odešlete soubor certifikátu pro podepsání zásad a klikněte na **Přidat**. [Tady](/azure/attestation/policy-signer-examples) najdete příklady.

### <a name="delete-policy-signer-certificate"></a>Odstranit certifikát podepsané zásady

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **certifikáty podepisujících zásad** .
5.  V horní nabídce klikněte na **Odstranit** (tlačítko se zakáže pro poskytovatele ověření identity vytvořené bez požadavku na podepsání zásady).
6.  Odešlete soubor certifikátu pro podepsání zásad a klikněte na **Odstranit**. [Tady](/azure/attestation/policy-signer-examples) najdete příklady. 

## <a name="attestation-policy"></a>Zásady ověřování identity

### <a name="view-attestation-policy"></a>Zobrazit zásady ověření identity

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **zásady** .
5.  Vyberte preferovaný **typ ověření identity** a zobrazte **aktuální zásady** .

### <a name="configure-attestation-policy"></a>Konfigurace zásad ověření identity

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Když se vytvoří poskytovatel ověření identity bez požadavku na podepsání zásady

##### <a name="upload-policy-in-jwt-format"></a>Odeslat zásady ve formátu JWT

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **zásady** .
5.  V horní nabídce klikněte na **Konfigurovat** .
6.  Když se poskytovatel ověření identity vytvoří bez požadavku na podepisování zásad, může uživatel nahrát zásadu v typu **JWT** nebo v **textovém** formátu.
7.  Vybrat **Formát zásad** jako **JWT**
8.  Nahrajte soubor zásad s obsahem zásady ve formátu **nepodepsaného nebo podepsaného JWT** a klikněte na **Uložit**. [Tady](/azure/attestation/policy-examples) najdete příklady.
    
    V případě možnosti nahrání souboru se verze Preview zobrazí v textovém formátu a verze Preview není upravitelná.

7.  Kliknutím na **aktualizovat** v horní nabídce zobrazíte nakonfigurovanou zásadu.

##### <a name="upload-policy-in-text-format"></a>Nahrát zásady v textovém formátu

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **zásady** .
5.  V horní nabídce klikněte na **Konfigurovat** .
6.  Když se poskytovatel ověření identity vytvoří bez požadavku na podepisování zásad, může uživatel nahrát zásadu v typu **JWT** nebo v **textovém** formátu.
7.  Vybrat **Formát zásad** jako **text**
8.  Nahrajte soubor zásad s obsahem v **textovém** formátu nebo zadejte obsah zásady do textové oblasti a klikněte na **Uložit**. [Tady](/azure/attestation/policy-examples) najdete příklady.

    V případě možnosti nahrání souboru se verze Preview zobrazí v textovém formátu a verze Preview není upravitelná.

8.  Kliknutím na **aktualizovat** zobrazíte nakonfigurovanou zásadu.

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Když se vytvoří poskytovatel ověření identity s požadavkem na podepsání zásad

##### <a name="upload-policy-in-jwt-format"></a>Odeslat zásady ve formátu JWT

1.  V nabídce Azure Portal nebo na domovské stránce vyberte **všechny prostředky** .
2.  Do pole Filtr zadejte název poskytovatele ověření identity.
3.  Vybrat poskytovatele ověření identity a přejít na stránku Přehled
4.  V nabídce prostředků na levé straně nebo v dolním podokně klikněte na **zásady** .
5.  V horní nabídce klikněte na **Konfigurovat** .
6.  Když se poskytovatel ověření identity vytvoří s požadavkem na podepsání zásad, může uživatel nahrávat zásady jenom ve **formátu podepsaného JWT** .
7.  Nahrání souboru zásad je **podepsaný formátem JWT** a klikněte na **Uložit**. [Tady](/azure/attestation/policy-examples) najdete příklady.

    V případě možnosti nahrání souboru se verze Preview zobrazí v textovém formátu a verze Preview není upravitelná.
    
8.  Kliknutím na **aktualizovat** zobrazíte nakonfigurovanou zásadu.

 










