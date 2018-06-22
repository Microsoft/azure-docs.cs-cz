### <a name="prerequisites"></a>Požadavky
* Účet Twilio
* Ověřené telefonní číslo Twilio, který může přijímat zprávy SMS
* Ověřené Twilio telefonní číslo, které může poslat SMS

> [!NOTE]
> Pokud používáte zkušební účet Twilio, lze odeslat pouze SMS k **ověřit** telefonních čísel.  
> 
> 

Než účtu Twilio můžete použít v aplikaci logiky, musíte je nejdříve autorizovat aplikaci logiky se připojit ke svému účtu Twilio. Naštěstí můžete k tomu snadno z v rámci aplikace logiky na portálu Azure. 

Tady jsou kroky k autorizaci aplikace logiky pro připojení k účtu Twilio:

1. Chcete-li vytvořit připojení k Twiliu, v návrháři aplikace logiky, vyberte **zobrazit Microsoft spravované rozhraní API** v rozevíracím seznamu zadejte *Twilio* do vyhledávacího pole. Vyberte aktivační události nebo akci, kterou budete chtít použít:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Pokud jste nevytvořili žádné připojení k Twiliu před, budete získat zobrazí výzva k zadání přihlašovacích údajů vaší Twilio. Tyto přihlašovací údaje se použije k autorizaci aplikace logiky pro připojení k a přístup k datům účtu Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Budete potřebovat **id účtu Twilio** a **Twilio přístupový token** z řídicího panelu Twilio, takže přihlásit k účtu Twilio nyní a získat tyto dva údaje:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. K identifikaci tyto dvě údaje Twilio a logiku aplikace použít odlišné názvy. Zde je, jak je nutné mapovat do dialogového okna aplikace logiky: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Vyberte **vytvořit připojení** tlačítko:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Všimněte si vytvořil připojení a je nyní můžete pokračovat v dalších krocích v aplikaci logiky:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

