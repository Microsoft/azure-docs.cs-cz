---
title: Jak chcete-li vrátit X.509 certifikáty v Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Jak chcete-li vrátit certifikáty X.509 s vaší instancí služby Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: a8ba667e6af316620d7a8530f29a6640edada13d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054638"
---
# <a name="how-to-roll-x509-device-certificates"></a>Jak chcete-li vrátit zařízení certifikáty X.509

Během životního cyklu řešení IoT musíte vrátit certifikáty. Dva hlavní důvody pro certifikáty se zajištěním provozu by bezpečnosti a vypršení platnosti certifikátů. 

Certifikáty se zajištěním provozu je osvědčeným postupem zabezpečení k zabezpečení vašeho systému v případě porušení zabezpečení. Jako součást [Předpokládejme metodologie porušení](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft nejzávažnějších potřebu s procesy reaktivní zabezpečení na místě spolu s preventivní opatření. Vaše zařízení certifikáty se zajištěním provozu by měl být součástí tyto procesy zabezpečení. Frekvence, ve kterém bude možné certifikáty bude záviset na požadavky na zabezpečení vašeho řešení. Zákazníci s řešeními týkajících se vysoce citlivá data může vrátit certifikátu každý den, zatímco ostatní vrátit své certifikáty každých několik let.

Aktualizace certifikátu uložené na zařízení a služby IoT hub bude zahrnovat certifikáty zařízení se zajištěním provozu. Později, můžete zařízení zmenšil sama s IoT hubu pomocí normální [automatického zřizování](concepts-auto-provisioning.md) službě Device Provisioning.


## <a name="obtain-new-certificates"></a>Získat nové certifikáty

Existuje mnoho způsobů, jak získat nové certifikáty pro zařízení IoT. Patří mezi ně získání certifikátů od výrobce zařízení, generování vlastní certifikáty a třetí strany s spravovat vytváření certifikátu za vás. 

Certifikáty jsou podepsané podle sebe tvoří řetěz certifikátů od kořenové certifikační Autority certifikátu ke [listový certifikát](concepts-security.md#end-entity-leaf-certificate). Podpisový certifikát se certifikát použitý k podpisu listový certifikát na konci řetěz certifikátů. Podpisový certifikát může být kořenový certifikát CA nebo zprostředkující certifikát v řetězu certifikátů. Další informace najdete v tématu [certifikáty X.509](concepts-security.md#x509-certificates).
 
Získejte podpisový certifikát dvěma různými způsoby. Prvním způsobem, který se doporučuje pro produkční systémy, je koupit si podpisový certifikát od kořenové certifikační autority (CA). Tímto způsobem zřetězený zabezpečení na důvěryhodný zdroj. 

Druhý způsob je vytvořit vlastní certifikátů X.509 pomocí některého nástroje, například OpenSSL. Tento přístup se skvěle hodí pro testování certifikáty X.509, ale poskytuje několik záruku týkající se zabezpečení. Doporučujeme, abyste že použijete tento přístup pouze pro účely testování, pokud jste si připravili tak, aby fungoval jako poskytovatele certifikační Autority.
 

## <a name="roll-the-certificate-on-the-device"></a>Vrácení certifikát na zařízení

Certifikáty do zařízení by měl vždy být uloženy na bezpečném místě, jako je [modulu hardwarového zabezpečení (HSM)](concepts-device.md#hardware-security-module). Způsob, jakým bude možné certifikáty zařízení bude záviset na jak byly vytvořeny a nainstalované v zařízení na prvním místě. 

Pokud jste se dostali certifikáty od třetí strany, musíte prozkoumat, jak vrátit změny své certifikáty. Proces může být součástí vaší uspořádání s nimi, nebo může být samostatná služba, které nabízejí. 

Pokud spravujete vlastní certifikáty zařízení, budete muset vytvořit vlastní kanál pro aktualizace certifikátů. Ujistěte se, že původní a nové certifikáty listu mají stejný běžný název (CN). Tím, že stejné CN zařízení můžete znova nezajistíte samotný bez vytvoření duplicitní registrační záznam.


## <a name="roll-the-certificate-in-the-iot-hub"></a>Vrátit certifikátu ve službě IoT hub

Certifikát zařízení můžete ručně přidat do služby IoT hub. Certifikát je také možné automatizovat pomocí instance služby Device Provisioning. V tomto článku budeme předpokládat, že instance služby Device Provisioning se používá pro podporu automatického zřizování.

Když zařízení je zpočátku zřídit prostřednictvím automatického zřizování, se spustí up a kontaktuje službu zřizování. Zřizovací služba reaguje pomocí provádí kontrolu identity před vytvořením identitu zařízení v IoT hubu pomocí zařízení listový certifikát jako přihlašovací údaje. Zřizovací služba pak říká zařízení, kterému IoT hubu je přiřazen, a jeho listový certifikát zařízení potom použije k ověření a připojení ke službě IoT hub. 

Jakmile byla vrácena nového listového certifikátu do zařízení, se můžete už připojit ke službě IoT hub protože ho používá nový certifikát pro připojení. IoT hub rozpozná pouze zařízení s starého certifikátu. Výsledek pokusu o připojení zařízení bude chybu "neoprávněné" připojení. Chcete-li vyřešit tuto chybu, je nutné aktualizovat položku registrace pro zařízení pro zařízení nového listového certifikátu. Pak službu zřizování můžete aktualizovat podle potřeby je znovu zajištěny zařízení informace registru zařízení služby IoT Hub. 

Jedinou možnou výjimkou k tomuto selhání připojení bude scénář, ve kterém jste vytvořili [skupinu registrací](concepts-service.md#enrollment-group) pro vaše zařízení ve zřizovací službě. V takovém případě pokud kořenový nebo zprostředkující certifikáty nejsou zavádění v zařízení řetěz certifikátů, pak zařízení bude rozpoznán, pokud je součástí řetězce důvěryhodnosti definované ve skupině pro registraci nového certifikátu. V případě tohoto scénáře jako reakce na porušení zabezpečení by měl aspoň blokovaných konkrétní zařízení certifikáty ve skupině, které jsou považovány za být dojde k porušení zabezpečení. Další informace najdete v tématu [seznam zakázaných konkrétní zařízení ve skupině pro registraci](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Aktualizuje se registrace položky pro souhrn certifikátů se provádí na **Správa registrací** stránky. Pro přístup k této stránce, postupujte podle těchto kroků:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do instance IoT Hub Device Provisioning Service, která má položku registrace pro vaše zařízení.

2. Klikněte na **Správa registrací**.

    ![Spravovat registrace](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Jak zpracovat aktualizaci položky registrace bude záviset na tom, jestli používáte jednotlivé registrace nebo skupinových registrací. Také doporučené postupy se liší v závislosti na tom, jestli máte postupný certifikáty z důvodu porušení zabezpečení nebo vypršení platnosti certifikátu. Následující části popisují, jak zpracovat tyto aktualizace.


## <a name="individual-enrollments-and-security-breaches"></a>Jednotlivé registrace a porušení zabezpečení

Pokud máte postupný certifikáty v reakci na porušení zabezpečení, měli byste použít následující postup, který okamžitě odstraní aktuální certifikát:

1. Klikněte na tlačítko **jednotlivé registrace**a klikněte na položku ID registrace v seznamu. 

2. Klikněte na tlačítko **odstranit aktuální certifikát** tlačítko a pak klikněte na ikonu složky a vyberte nový certifikát k odeslání pro položku registrace. Klikněte na tlačítko **Uložit** po dokončení.

    Tyto kroky by měla provést pro primární a sekundární certifikát, pokud jsou obě dojde k narušení.

    ![Spravovat jednotlivé registrace](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Po ohrožení zabezpečení certifikátu je odebraná ze zřizovací služby, přejděte do služby IoT hub a odebrat registraci zařízení spojenou s certifikátem ohrožení zabezpečení.     

    ![Odebrání registrace zařízení služby IoT hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)


## <a name="individual-enrollments-and-certificate-expiration"></a>Jednotlivé registrace a vypršení platnosti certifikátu

Pokud máte postupný certifikáty ke zpracování vypršení platnosti certifikátů, by vám konfigurace sekundární certifikát následujícím způsobem snížit prostoje pro zařízení s pokusem o zřízení.

Později při sekundárního certifikátu také přiblíží konec platnosti a musí být vrácena, můžete otočit pomocí primární konfiguraci. Otočení mezi primární a sekundární certifikát tímto způsobem zkracuje dobu výpadku pro zařízení s pokusem o zřízení.


1. Klikněte na tlačítko **jednotlivé registrace**a klikněte na položku ID registrace v seznamu. 

2. Klikněte na tlačítko **sekundární certifikát** a pak klikněte na ikonu složky a vyberte nový certifikát k odeslání pro položku registrace. Klikněte na **Uložit**.

    ![Spravovat jednotlivé registrace pomocí sekundární certifikát](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Dále pokud vypršela platnost primárního certifikátu, vraťte se a odstranit tento primární certifikát klepnutím **odstranit aktuální certifikát** tlačítko.

## <a name="enrollment-groups-and-security-breaches"></a>Skupiny registrací a porušení zabezpečení

Pokud chcete aktualizovat skupinovou registraci v reakci na porušení zabezpečení, používejte jeden z následujících dvou přístupů, které okamžitě odstranit aktuální kořenové certifikační Autority nebo certifikát zprostředkující.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualizovat ohrožení zabezpečení kořenové Certifikační autority

1. Klikněte na tlačítko **certifikáty** kartu pro instanci služby Device Provisioning.

2. Klikněte na ohrožení zabezpečení certifikátu v seznamu a klikněte **odstranit** tlačítko. Potvrďte odstranění zadáním názvu certifikátu a klikněte na tlačítko **OK**. Tento postup opakujte pro všechny ohroženými certifikáty.

    ![Odstranit certifikát kořenové certifikační Autority](./media/how-to-roll-certificates/delete-root-cert.png)

3. Postupujte podle kroků uvedených v [konfigurovat ověřit certifikáty certifikační Autority](how-to-verify-certificates.md) pro přidání a ověření nové certifikáty kořenové certifikační Autority.

4. Klikněte na tlačítko **Správa registrací** kartu pro instanci služby Device Provisioning a klikněte na tlačítko **skupiny registrací** seznamu. Klikněte na název skupiny registrací v seznamu.

5. Klikněte na tlačítko **certifikát certifikační Autority**a vyberte váš nový certifikát kořenové certifikační Autority. Potom klikněte na **Uložit**. 

    ![Vyberte nový kořenový certifikát certifikační Autority](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Po ohrožení zabezpečení certifikátu je odebraná ze zřizovací služby, přejděte na propojené Centrum IoT, která obsahuje registrace ohroženého zařízení a odebrání registrace spojenou s certifikátem ohrožení zabezpečení.

    ![Odebrání registrace zařízení služby IoT hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)


#### <a name="update-compromised-intermediate-certificates"></a>Aktualizace dojde k ohrožení bezpečnosti zprostředkující certifikáty

1. Klikněte na tlačítko **skupiny registrací**a potom klikněte na název skupiny v seznamu. 

2. Klikněte na tlačítko **zprostředkující certifikát**, a **odstranit aktuální certifikát**. Klikněte na ikonu složky a přejděte na nový zprostředkující certifikát k odeslání pro skupiny registrací. Klikněte na tlačítko **Uložit** až budete hotoví. Tyto kroky by měla provést pro obě primární a sekundární certifikát, pokud jsou obě dojde k narušení.

    Tento nový zprostředkující certifikát by měl být podepsány ověřené kořenový certifikát certifikační Autority, která již byla přidána do služby zřizování. Další informace najdete v tématu [certifikáty X.509](concepts-security.md#x509-certificates).

    ![Spravovat jednotlivé registrace](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Po ohrožení zabezpečení certifikátu je odebraná ze zřizovací služby, přejděte na propojené Centrum IoT, která obsahuje registrace zařízení a odebrat registraci spojenou s certifikátem ohrožení zabezpečení.

    ![Odebrání registrace zařízení služby IoT hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)


## <a name="enrollment-groups-and-certificate-expiration"></a>Skupiny registrací a vypršení platnosti certifikátu

Pokud certifikáty ke zpracování vypršení platnosti certifikátu se zajištěním provozu, by měl použít konfiguraci sekundární certifikát následujícím způsobem zajistit bez výpadku provozu zařízení došlo k pokusu o zřízení.

Později při sekundárního certifikátu také přiblíží konec platnosti a musí být vrácena, můžete otočit pomocí primární konfiguraci. Otočení mezi primární a sekundární certifikát tímto způsobem zajišťuje bez výpadku provozu zařízení došlo k pokusu o zřízení. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualizovat certifikáty kořenové certifikační Autority, u nichž vyprší platnost

1. Postupujte podle kroků uvedených v [konfigurovat ověřit certifikáty certifikační Autority](how-to-verify-certificates.md) pro přidání a ověření nové certifikáty kořenové certifikační Autority.

2. Klikněte na tlačítko **Správa registrací** kartu pro instanci služby Device Provisioning a klikněte na tlačítko **skupiny registrací** seznamu. Klikněte na název skupiny registrací v seznamu.

3. Klikněte na tlačítko **certifikát certifikační Autority**a vyberte váš nový certifikát kořenové certifikační Autority v části **sekundární certifikát** konfigurace. Potom klikněte na **Uložit**. 

    ![Vyberte nový kořenový certifikát certifikační Autority](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Později při vypršela platnost primárního certifikátu, klikněte na tlačítko **certifikáty** kartu pro instanci služby Device Provisioning. Klikněte certifikát s prošlou platností v seznamu a klikněte **odstranit** tlačítko. Potvrďte odstranění zadáním názvu certifikátu a klikněte na **OK**.

    ![Odstranit certifikát kořenové certifikační Autority](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualizace, u nichž vyprší platnost zprostředkující certifikáty


1. Klikněte na tlačítko **skupiny registrací**a klikněte na název skupiny v seznamu. 

2. Klikněte na tlačítko **sekundární certifikát** a pak klikněte na ikonu složky a vyberte nový certifikát k odeslání pro položku registrace. Klikněte na **Uložit**.

    Tento nový zprostředkující certifikát by měl být podepsány ověřené kořenový certifikát certifikační Autority, která již byla přidána do služby zřizování. Další informace najdete v tématu [certifikáty X.509](concepts-security.md#x509-certificates).

   ![Spravovat jednotlivé registrace pomocí sekundární certifikát](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Dále pokud vypršela platnost primárního certifikátu, vraťte se a odstranit tento primární certifikát klepnutím **odstranit aktuální certifikát** tlačítko.


## <a name="reprovision-the-device"></a>Znova zajistěte zařízení

Jakmile certifikát se převádí na zařízení a služby Device Provisioning, zařízení můžete znova nezajistíte samotné kontaktováním služby Device Provisioning. 

Jeden snadný způsob programovací zařízení na bázi je program zařízení ke kontaktování služby zřizování absolvovat toku zřizování, pokud zařízení obdrží chybu "neoprávněné" nepokoušel připojit ke službě IoT hub.

Dalším způsobem je na staré i nové certifikáty platné pro krátké překrytí, a pomocí služby IoT hub do zařízení odeslat příkaz ho znovu zaregistrovat prostřednictvím služby zřizování aktualizovat informace o připojení služby IoT Hub. Protože každé zařízení může zpracovat příkazy odlišně, budete muset programu zařízení vědět, co dělat, když uživatel vyvolá příkaz. Existuje několik způsobů, jak můžete přikázat zařízení prostřednictvím služby IoT Hub, a doporučujeme používat [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) nebo [úlohy](../iot-hub/iot-hub-devguide-jobs.md) k zahájení procesu.

Po dokončení neukončil zařízení budou moct připojit ke službě IoT Hub pomocí jejich nové certifikáty.


## <a name="blacklist-certificates"></a>Certifikáty blacklist

V reakci na porušení zabezpečení budete muset seznam zakázaných certifikát zařízení. Na seznam zakázaných zařízení certifikát, zakážete položku registrace pro cílového zařízení/certifikát. Další informace najdete v tématu Adaptivně zařízení [zrušení registrace spravovat](how-to-revoke-device-access-portal.md) článku.

Jakmile se certifikát zahrnuté jako součást položky zakázané registrace, všechny pokusy o registraci pomocí IoT hub, které certifikáty se nezdaří, i v případě, že je povolená jako součást jiné položky registrace.
 



## <a name="next-steps"></a>Další postup

- Další informace o certifikátech X.509 do služby Device Provisioning najdete v tématu [zabezpečení](concepts-security.md) 
- Další informace o tom, jak provést ověření vlastnictví pro certifikáty webu X.509 s Azure IoT Hub Device Provisioning Service, najdete v článku [ověření certifikátů](how-to-verify-certificates.md)
- Další informace o tom, jak vytvořit skupinu registrací pomocí portálu, najdete v článku [Správa registrací zařízení pomocí webu Azure portal](how-to-manage-enrollments.md).










