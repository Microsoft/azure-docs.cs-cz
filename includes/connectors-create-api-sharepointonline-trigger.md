V tomto příkladu I vám ukáže, jak používat **SharePoint Online - při vytvoření nové položky** aktivační událost zahájíte pracovní postup aplikace logiky při vytvoření nové položky v seznamu SharePoint Online.

> [!NOTE]
> Získat zobrazí výzva k přihlášení k účtu služby SharePoint, pokud jste ještě nevytvořili *připojení* ke službě SharePoint Online.  
> 
> 

1. Zadejte *sharepoint* do vyhledávacího pole v designeru aplikace logiky zvolte **SharePoint Online - při vytvoření nové položky** aktivační události.  
   ![SharePoint online aktivace image ](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. **Při vytvoření nové položky** ovládací prvek je zobrazen.  
   ![SharePoint online aktivace obrázek 2](./media/connectors-create-api-sharepointonline/trigger-2.png)   
3. Vyberte **URL webu**. Toto je web služby SharePoint online, kterou chcete sledovat pro nové položky se spustit pracovní postup.  
   ![SharePoint online aktivace image 3](./media/connectors-create-api-sharepointonline/trigger-3.png)   
4. Vyberte **název seznamu**. Toto je seznam na webu služby SharePoint Online, které chcete sledovat pro nové položky, které aktivují pracovní postup.  
   ![SharePoint online aktivace obrázek 4](./media/connectors-create-api-sharepointonline/trigger-4.png)   

Aplikace logiky v tomto okamžiku je nakonfigurovaná s aktivační událost, která zahájí spuštění ostatních triggery a akce v pracovním postupu. Bude to trvat místní pokaždé, když se vytvoří novou položku v seznamu SharePoint Online, které jste vybrali.  

