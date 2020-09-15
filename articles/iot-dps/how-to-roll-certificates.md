---
title: Navrácení certifikátů X. 509 v Azure IoT Hub Device Provisioning Service
description: Postup nasazení certifikátů X. 509 pomocí instance služby Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: fbcb3656bc824e2fd352f92314652bd04167b4d8
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531402"
---
# <a name="how-to-roll-x509-device-certificates"></a>Postup nasazení certifikátů zařízení X. 509

Během životního cyklu řešení IoT budete muset certifikáty navýšit. Dvěma hlavními důvody pro zavedení certifikátů je porušení zabezpečení a vypršení platnosti certifikátů. 

Průběžné certifikáty jsou osvědčenými postupy zabezpečení, které vám pomůžou zabezpečit systém v případě porušení. V rámci této [metodiky předpokládáme](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), že společnost Microsoft vyžaduje, aby byly v zavedeny reaktivní procesy zabezpečení spolu s preventivními opatřeními. Zavedení certifikátů zařízení by mělo být součástí těchto procesů zabezpečení. Četnost změn certifikátů bude záviset na potřebách zabezpečení vašeho řešení. Zákazníci s řešeními, které zahrnují velmi citlivá data, můžou každý den nastavovat certifikát, zatímco ostatní si své certifikáty každých pár let.

Certifikáty se značným zařízením budou zahrnovat aktualizaci certifikátu uloženého v zařízení a ve službě IoT Hub. Následně se může zařízení znovu zřídit se službou IoT Hub s využitím normálního [zřizování](about-iot-dps.md#provisioning-process) se službou Device Provisioning (DPS).


## <a name="obtain-new-certificates"></a>Získat nové certifikáty

K dispozici je celá řada způsobů, jak získat nové certifikáty pro vaše zařízení IoT. Mezi ně patří získání certifikátů z továrny zařízení, generování vlastních certifikátů a Správa vytvoření certifikátu třetí stranou. 

Certifikáty jsou vzájemně podepsané, aby tvořily řetěz důvěryhodnosti od kořenového certifikátu certifikační autority až po [listový certifikát](concepts-x509-attestation.md#end-entity-leaf-certificate). Podpisový certifikát je certifikát použitý k podepsání listového certifikátu na konci řetězce důvěry. Podpisový certifikát může být kořenový certifikát CA nebo zprostředkující certifikát v řetězu důvěryhodnosti. Další informace najdete v tématu [certifikáty X. 509](concepts-x509-attestation.md#x509-certificates).
 
Podpisový certifikát můžete získat dvěma různými způsoby. Prvním způsobem, který se doporučuje pro produkční systémy, je koupit podpisový certifikát od kořenové certifikační autority (CA). Tímto způsobem jsou zabezpečení zřetězené až k důvěryhodnému zdroji. 

Druhým způsobem je vytvořit vlastní certifikáty X. 509 pomocí nástroje, jako je OpenSSL. Tento přístup je skvělý pro testování certifikátů X. 509, ale poskytuje několik záruk týkajících se zabezpečení. Tento přístup doporučujeme použít jenom pro testování, pokud jste se připravili jako poskytovatel vaší certifikační autority.
 

## <a name="roll-the-certificate-on-the-device"></a>Vyveďte certifikát na zařízení.

Certifikáty na zařízení by se měly vždycky ukládat na bezpečné místo, jako je [modul hardwarového zabezpečení (HSM)](concepts-service.md#hardware-security-module). Způsob, jakým povedete certifikáty zařízení, bude záviset na tom, jak byly vytvořeny a nainstalovány v zařízeních na prvním místě. 

Pokud máte certifikáty od třetí strany, musíte si vymezit, jak si vyžádají jejich certifikáty. Tento proces může být součástí vašeho ujednání a může se jednat o samostatnou službu, kterou nabízí. 

Pokud spravujete vlastní certifikáty zařízení, budete muset vytvořit vlastní kanál pro aktualizaci certifikátů. Zajistěte, aby měly staré i nové listové certifikáty stejný běžný název (CN). Když máte stejnou propojenou síť, zařízení se může znovu zřídit bez vytvoření duplicitního registračního záznamu. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Zavedení certifikátu ve službě IoT Hub

Certifikát zařízení je možné přidat do služby IoT Hub ručně. Certifikát je také možné automatizovat pomocí instance služby Device Provisioning. V tomto článku budeme předpokládat, že se instance služby Device Provisioning používá k podpoře automatického zřizování.

Když se zařízení zpočátku zřídí prostřednictvím automatického zřizování, spustí se a spojí službu zřizování. Služba zřizování reaguje tím, že před vytvořením identity zařízení ve službě IoT Hub pomocí certifikátu na listovém zařízení jako přihlašovacích údajů vykoná kontrolu identity. Služba zřizování pak sdělí zařízení, ke kterému je přidruženo služba IoT Hub, a zařízení pak použije svůj listový certifikát k ověření a připojení ke službě IoT Hub. 

Jakmile se nový listový certifikát převede do zařízení, už se nemůže připojit ke službě IoT Hub, protože se k připojení používá nový certifikát. Centrum IoT rozpoznává zařízení jenom se starým certifikátem. Výsledkem pokusu o připojení zařízení bude chyba neautorizovaného připojení. Pokud chcete tuto chybu vyřešit, musíte aktualizovat položku registrace pro zařízení na účet pro nový listový certifikát zařízení. Služba zřizování pak může podle potřeby aktualizovat informace registru IoT Hub zařízení, když se zařízení znovu zřídí. 

Jednou z možných výjimek k této chybě připojení by byl scénář, ve kterém jste ve službě zřizování vytvořili [skupinu pro registraci](concepts-service.md#enrollment-group) zařízení. V takovém případě, pokud se v řetězu certifikátů zařízení neúčtují kořenové nebo zprostředkující certifikáty, bude zařízení rozpoznané, pokud je nový certifikát součástí řetězce důvěryhodnosti definovaného ve skupině pro registraci. Pokud k tomuto scénáři dojde v reakci na porušení zabezpečení, měli byste aspoň zakázat konkrétní certifikáty zařízení ve skupině, které jsou považovány za porušení. Další informace najdete v tématu [zákaz konkrétních zařízení ve skupině pro registraci](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#disallow-specific-devices-in-an-enrollment-group).

Aktualizace položek registrace pro zahrnuté certifikáty se provádí na stránce **spravovat registrace** . Pro přístup k této stránce použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci IoT Hub Device Provisioning Service, která má položku registrace pro vaše zařízení.

2. Klikněte na **Správa registrací**.

    ![Spravovat registrace](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Způsob, jakým se aktualizuje záznam registrace, bude záviset na tom, jestli používáte jednotlivé registrace, nebo když chcete seskupit registraci. Doporučené postupy se také liší v závislosti na tom, zda se jedná o certifikáty z důvodu porušení zabezpečení nebo vypršení platnosti certifikátu. Následující části popisují, jak tyto aktualizace zpracovat.


## <a name="individual-enrollments-and-security-breaches"></a>Jednotlivé registrace a porušení zabezpečení

Pokud v reakci na porušení zabezpečení provádíte průběžné certifikáty, měli byste použít následující postup, který okamžitě odstraní aktuální certifikát:

1. Klikněte na **jednotlivé registrace**a v seznamu klikněte na položku ID registrace. 

2. Klikněte na tlačítko **Odstranit aktuální certifikát** a potom klikněte na ikonu složky a vyberte nový certifikát, který se má nahrát pro položku registrace. Po dokončení klikněte na **Uložit** .

    Tyto kroky by se měly dokončit u primárního a sekundárního certifikátu, pokud dojde k ohrožení obou z nich.

    ![Spravovat jednotlivé registrace s porušením zabezpečení](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Po odebrání napadeného certifikátu ze služby zřizování se certifikát může i nadále používat k tomu, aby se zařízení připojilo ke službě IoT Hub, pokud zde existuje registrace zařízení pro něj. Tyto dva způsoby můžete vyřešit: 

    Prvním způsobem je ruční navigace do služby IoT Hub a okamžité odebrání registrace zařízení přidružené k ohroženému certifikátu. Když zařízení znovu zřídí aktualizovaný certifikát, vytvoří se nová registrace zařízení.     

    ![Odebrání registrace zařízení IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druhý způsob, jak pomocí opětovného zajišťování znovu zřídit zařízení do stejného centra IoT Hub. Tento přístup se dá použít k nahrazení certifikátu pro registraci zařízení ve službě IoT Hub. Další informace najdete v tématu [jak znovu zřídit zařízení](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Jednotlivé registrace a vypršení platnosti certifikátu

Pokud vytváříte certifikáty pro zpracování vypršení platnosti certifikátů, měli byste pomocí sekundární konfigurace certifikátu zkrátit dobu výpadku zařízení, která se snaží zřídit.

Později, když se sekundární certifikát taky blíží vypršení platnosti a je potřeba ho navrátit, můžete ho použít k primární konfiguraci. Otáčení mezi primárním a sekundárním certifikátem tímto způsobem zkracuje výpadky zařízení, která se snaží zřídit.


1. Klikněte na **jednotlivé registrace**a v seznamu klikněte na položku ID registrace. 

2. Klikněte na **sekundární certifikát** a potom klikněte na ikonu složky a vyberte nový certifikát, který se má nahrát pro položku registrace. Klikněte na **Uložit**.

    ![Spravovat jednotlivé registrace pomocí konce platnosti sekundárního certifikátu](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Později po vypršení platnosti primárního certifikátu se vraťte a odstraňte tento primární certifikát kliknutím na tlačítko **Odstranit aktuální certifikát** .

## <a name="enrollment-groups-and-security-breaches"></a>Skupiny registrací a porušení zabezpečení

Pokud chcete aktualizovat registraci skupiny v reakci na porušení zabezpečení, měli byste použít jeden z následujících přístupů, který okamžitě odstraní aktuální kořenovou certifikační autoritu nebo zprostředkující certifikát.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualizace zabezpečení certifikátů kořenové certifikační autority

1. Klikněte na kartu **certifikáty** pro instanci služby Device Provisioning.

2. Klikněte na ohrožený certifikát v seznamu a potom klikněte na tlačítko **Odstranit** . Potvrďte odstranění zadáním názvu certifikátu a klikněte na **OK**. Tento postup opakujte pro všechny ohrožené certifikáty.

    ![Odstranit certifikát kořenové certifikační autority](./media/how-to-roll-certificates/delete-root-cert.png)

3. Postupujte podle kroků uvedených v částech [Konfigurace ověřených certifikátů certifikační autority](how-to-verify-certificates.md) a přidejte a ověřte nové kořenové certifikáty certifikační autority.

4. Klikněte na kartu **spravovat registrace** pro instanci služby Device Provisioning a pak klikněte na seznam **skupiny** registrací. V seznamu klikněte na název skupiny zápisu.

5. Klikněte na **certifikát certifikační autority**a vyberte nový certifikát kořenové certifikační autority. Potom klikněte na **Uložit**. 

    ![Vyberte certifikát nové kořenové certifikační autority pro ohrožený certifikát.](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Po odebrání napadeného certifikátu ze služby zřizování se certifikát může i nadále používat k tomu, aby se zařízení připojilo ke službě IoT Hub, pokud tam existují registrace zařízení, pro které existuje. Tyto dva způsoby můžete vyřešit: 

    Prvním způsobem je ruční navigace do služby IoT Hub a okamžité odebrání registrace zařízení přidružené k ohroženému certifikátu. Až se vaše zařízení znovu zřídí s aktualizovanými certifikáty, pro každou z nich se vytvoří nová registrace zařízení.     

    ![Odebrání registrace zařízení IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druhý způsob, jak pomocí opětovného zřizování znovu zřídit vaše zařízení se stejným centrem IoT Hub. Tento přístup se dá použít k nahrazení certifikátů pro registrace zařízení ve službě IoT Hub. Další informace najdete v tématu [jak znovu zřídit zařízení](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Aktualizace zabezpečení zprostředkujících certifikátů

1. Klikněte na **skupiny**registrací a potom v seznamu klikněte na název skupiny. 

2. Klikněte na **zprostředkující certifikát**a **odstraňte aktuální certifikát**. Klikněte na ikonu složky a přejděte k novému zprostředkujícímu certifikátu, který se má nahrát pro skupinu pro registraci. Až budete hotovi, klikněte na **Uložit** . Tyto kroky je třeba provést pro primární i sekundární certifikát, pokud dojde k ohrožení bezpečnosti obou.

    Tento nový zprostředkující certifikát by měl být podepsaný ověřenou kořenovým certifikátem certifikační autority, který už je přidaný do služby zřizování. Další informace najdete v tématu [certifikáty X. 509](concepts-x509-attestation.md#x509-certificates).

    ![Správa jednotlivých registrací pro napadený zprostředkující](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Po odebrání napadeného certifikátu ze služby zřizování se certifikát může i nadále používat k tomu, aby se zařízení připojilo ke službě IoT Hub, pokud tam existují registrace zařízení, pro které existuje. Tyto dva způsoby můžete vyřešit: 

    Prvním způsobem je ruční navigace do služby IoT Hub a okamžité odebrání registrace zařízení přidružené k ohroženému certifikátu. Až se vaše zařízení znovu zřídí s aktualizovanými certifikáty, pro každou z nich se vytvoří nová registrace zařízení.     

    ![Odebrání registrace zařízení IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druhý způsob, jak pomocí opětovného zřizování znovu zřídit vaše zařízení se stejným centrem IoT Hub. Tento přístup se dá použít k nahrazení certifikátů pro registrace zařízení ve službě IoT Hub. Další informace najdete v tématu [jak znovu zřídit zařízení](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Registrace skupin a vypršení platnosti certifikátu

Pokud vytváříte certifikáty pro zpracování vypršení platnosti certifikátů, měli byste použít sekundární konfiguraci certifikátu následujícím způsobem, aby se zajistilo, že se zařízení nepokusí zřídit bez výpadků.

Později, když se sekundární certifikát taky blíží vypršení platnosti a je potřeba ho navrátit, můžete ho použít k primární konfiguraci. Otáčení mezi primárním a sekundárním certifikátem tímto způsobem nezajišťuje žádné výpadky zařízení, která se pokoušejí zřídit. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualizace certifikátů kořenových certifikačních autorit do vypršení platnosti

1. Postupujte podle kroků uvedených v částech [Konfigurace ověřených certifikátů certifikační autority](how-to-verify-certificates.md) a přidejte a ověřte nové kořenové certifikáty certifikační autority.

2. Klikněte na kartu **spravovat registrace** pro instanci služby Device Provisioning a pak klikněte na seznam **skupiny** registrací. V seznamu klikněte na název skupiny zápisu.

3. Klikněte na **certifikát certifikační autority**a vyberte svůj nový kořenový certifikát certifikační autority pod konfigurací **sekundárního certifikátu** . Potom klikněte na **Uložit**. 

    ![Vyberte certifikát nové kořenové certifikační autority pro vypršení platnosti.](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Později po vypršení platnosti primárního certifikátu klikněte na kartu **certifikáty** pro instanci služby Device Provisioning. Klikněte na certifikát s vypršenou platností v seznamu a potom klikněte na tlačítko **Odstranit** . Potvrďte odstranění zadáním názvu certifikátu a klikněte na **OK**.

    ![Odstranit certifikát kořenové certifikační autority](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualizace s vypršenou platností zprostředkujících certifikátů


1. Klikněte na **skupiny**registrací a v seznamu klikněte na název skupiny. 

2. Klikněte na **sekundární certifikát** a potom klikněte na ikonu složky a vyberte nový certifikát, který se má nahrát pro položku registrace. Klikněte na **Uložit**.

    Tento nový zprostředkující certifikát by měl být podepsaný ověřenou kořenovým certifikátem certifikační autority, který už je přidaný do služby zřizování. Další informace najdete v tématu [certifikáty X. 509](concepts-x509-attestation.md#x509-certificates).

   ![Správa skupin registrací pomocí vypršení platnosti sekundárního certifikátu](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Později po vypršení platnosti primárního certifikátu se vraťte a odstraňte tento primární certifikát kliknutím na tlačítko **Odstranit aktuální certifikát** .


## <a name="reprovision-the-device"></a>Opětovné zřízení zařízení

Jakmile se certifikát zaregistruje na zařízení i ve službě Device Provisioning, zařízení se může znovu zřídit tím, že se obrátí na službu Device Provisioning. 

Jedním jednoduchým způsobem, jak se zařízení přizpůsobovat, je naprogramovat zařízení, aby se obrátilo na službu zřizování, aby procházela prostřednictvím procesu zřizování, pokud zařízení obdrží při pokusu o připojení ke službě IoT Hub chybu typu "Neautorizováno".

Další možností je, aby byly staré i nové certifikáty platné pro krátké překrytí, a pomocí služby IoT Hub odesílat příkazy do zařízení, aby se znovu zaregistrovaly přes službu zřizování a aktualizovaly informace o IoT Hub připojení. Vzhledem k tomu, že každé zařízení může zpracovávat příkazy odlišně, budete muset zařízení naprogramovat, abyste věděli, co dělat při vyvolání příkazu. K dispozici je několik způsobů, jak zařízení můžete pomocí IoT Hub. k zahájení procesu doporučujeme použít [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) nebo [úlohy](../iot-hub/iot-hub-devguide-jobs.md) .

Jakmile se opětovné zřízení dokončí, zařízení se budou moct připojit k IoT Hub pomocí jejich nových certifikátů.


## <a name="disallow-certificates"></a>Zakázat certifikáty

V reakci na porušení zabezpečení budete možná muset zakázat certifikát zařízení. Pokud chcete zakázat certifikát zařízení, zakažte položku registrace pro cílové zařízení nebo certifikát. Další informace najdete v článku nepovolení zařízení v článku [Správa zrušení registrace](how-to-revoke-device-access-portal.md) .

Jakmile je certifikát zahrnutý jako součást zakázané položky registrace, všechny pokusy o registraci ve službě IoT Hub pomocí těchto certifikátů selžou, i když budou povolené jako součást jiné položky registrace.
 



## <a name="next-steps"></a>Další kroky

- Další informace o certifikátech X. 509 ve službě Device Provisioning najdete v tématu [ověřování certifikátů x. 509.](concepts-x509-attestation.md) 
- Další informace o tom, jak ověřit vlastnictví certifikátů CA X. 509 pomocí Azure IoT Hub Device Provisioning Service, najdete v tématu [ověření certifikátů](how-to-verify-certificates.md) .
- Další informace o tom, jak pomocí portálu vytvořit skupinu registrací, najdete v tématu [Správa registrace zařízení pomocí Azure Portal](how-to-manage-enrollments.md).
