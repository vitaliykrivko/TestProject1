var stepNum = 1;
var page;
var browser;
var timeOut = 15000;
var delay = 1000;

function testWebDesktop(){
  var browserName = 'iexplore';//"chrome";    
  var pageUrl = 'https://templates.office.com/en-US/Resume-Professional-TM16412145';  
  var fileName = 'test3';
  var testText = 'testing demo';
  
  step('Open browser');
  openBrowser(browserName);
  browser = Sys.WaitBrowser(browserName);
  
  assert(browser.Exists, 'Internet Explorer is running.');
  
  step('Go to link');
  page = browser.ToUrl(pageUrl, timeOut);
  browser.BrowserWindow(0).Maximize();
  
  step('Download file');
  var filePath = downloadFile(fileName);
  var fileInfo = aqFileSystem.GetFileInfo(filePath);
  
  assert(aqFile.Exists(filePath), 'File is saved');
  
  step('Open file');
  openWordpad(filePath);
 
  step('Edit file');
  editWordPad(testText);
  insertPicture();
  
  step('Save file');
  saveWordPad();  
  
  step('Close file and browser');
  closeWordPad();
  closeBrowser();
  
  assert(fileInfo.DateCreated < fileInfo.DateLastModified, 'File is updated');  
  
  endTest(); 
}

//------- LOGGING ---------------------------
function step(value){
  Log.PopLogFolder();
  Log.AppendFolder("Step " + stepNum + " - " + value);
  stepNum ++;
}

function endTest(){
  Log.PopLogFolder();
  stepNum = 1;
}

//-------- MAPPING -------------------------
function openBrowser(browserName){
  browser = Browsers.Item(browserName);
  browser.Run();
  browser = Sys.WaitBrowser(browserName, timeOut);
  browser.WaitPage('*', timeOut);
}

function downloadFile(fileName){
  var xPath = "//a[@role = 'button' and contains(text(),'Download')]";
  var downloadBtn = page.FindChildByXPath(xPath);//page.FindChild(['contentText'],['Download'], 8);
  if(downloadBtn == null)
    downloadBtn = page.FindChildByXPath(xPath);
  
  click(downloadBtn);
  Delay(delay);

  var overrideXpath = "//a[@id = 'overridelink']";
  var overrideBtn = page.FindChildByXPath(overrideXpath);
  if(overrideBtn == null){
    Delay(delay);
    overrideBtn = page.FindChildByXPath(overrideXpath);
  }
  if(overrideBtn != null)
    click(overrideBtn);
  Delay(delay);
    
  var saveDropBtn = browser.FindChild(['ObjectType'],['DropDownButton'], 10);//browser.FindChild(['ObjectIdentifier'],['Save'],8).UIAObject(1);                
  if(saveDropBtn == null){
    Delay(delay);
    saveDropBtn = browser.FindChild(['ObjectType'],['DropDownButton'], 10);
  }
  click(saveDropBtn);
  Delay(delay);
  
  var saveAsBtn = browser.FindChild(['ObjectIdentifier'],['Save as'], 5);
  click(saveAsBtn);
  Delay(delay);
  
  var fileNameCombo = browser.Window("#32770", "Save As", 1).Window("DUIViewWndClassName", "", 1).Pane("Explorer Pane").Window("FloatNotifySink", "", 1).Window("ComboBox", "", 1); 
  var saveAsPopup = browser.FindChild(['WndCaption'],['Save As'], 2);
  var pathControl = saveAsPopup.FindChild(['WndCaption'],['*Address*'],8);
  var folderPath = pathControl.WndCaption.replace('Address: ','');//"Address: C:\\Users\\IEUser\\Downloads"
  
  var filePath = folderPath + '\\' + fileName + '.docx';
  fileNameCombo.SetText(fileName);
  
  var saveSaveAsBtn = Sys.Browser("iexplore").Window("#32770", "Save As", 1).Window("Button", "&Save", 1);
  click(saveSaveAsBtn);
  Delay(delay);

  var confirmBtn = Aliases.browser.dlgConfirmSaveAs.paneConfirmSaveAs.CtrlNotifySink.btnYes;
  if(confirmBtn.Exists)
    click(confirmBtn);  

  return filePath;
}

function openWordpad(filePath){
  TestedApps.wordpad.Run();
  var wordpad = Aliases.wordpad.WaitChild('*WordPadClass*', 3000);
  wordpad.WaitProperty('VisibleOnScreen', true, 2000);
  wordpad.Maximize();
    
  if(filePath != null){
    wordpad.Keys('~fo');
    var dialog = Aliases.wordpad.Window("#32770", "Open", 1).Window("ComboBoxEx32", "", 1).Window("ComboBox", "", 1);
    var openBtn = Aliases.wordpad.Window("#32770", "Open", 1).Window("Button", "&Open", 1);
    dialog.SetText(filePath);
    click(openBtn);
  }
  return wordpad;
}

function editWordPad(value){  
  var sheet = Aliases.wordpad.wndWordPadClass.Edit("Rich Text Window");//Aliases.wordpad.wndWordPadClass.Window("RICHEDIT50W", "", 1);
  sheet.Keys(value); //sheet.SetText(value);  
}

function insertPicture(){
  var filePath = 'C:\\Users\\Public\\Pictures\\Sample Pictures\\Desert.jpg';

  var pictureBtn = Aliases.wordpad.wndWordPadClass.UIRibbonDockTop.Ribbon.Ribbon.NUIPane.propertypageRibbon.Pane("Lower Ribbon").Client(0).PropertyPage("Home").ToolBar("Insert").Grouping("Picture").SplitButton("Picture");
  pictureBtn.DblClick();
  Delay(delay);
  var fileNameCombo = Aliases.wordpad.Window("#32770", "Select Picture", 1).Window("ComboBoxEx32", "", 1).Window("ComboBox", "", 1);
  
  fileNameCombo.SetText(filePath);
  fileNameCombo.Keys('[Enter]');
}

function saveWordPad(){
  var wordpad = Aliases.wordpad.WaitChild('*WordPadClass*', 3000);
  wordpad.Keys('~fs');
  Delay(delay);
  
  var saveBtn = Aliases.wordpad.dlgWordPad.paneWordpad.CtrlNotifySink.btnSave;
  if(saveBtn.Exists){
    Log.Message('Save');
    click(saveBtn);
  }
  Delay(delay);
}

function saveAsWordPad(){
  var wordpad = Aliases.wordpad.WaitChild('*WordPadClass*', 3000);
  wordpad.Keys('~fs');
  Delay(delay);
  var saveBtn = Aliases.wordpad.Window("#32770", "Save As", 1).Window("Button", "&Save", 2);
  if(saveBtn.Exists){
    Log.Message('Save');
    click(saveBtn);
  }
}

function closeWordPad(){
 Aliases.wordpad.Terminate();
}

function closeBrowser(){
  Aliases.browser.Terminate();
}

//------- HELPERS -------------------------
function assert(bool, text){
  if (bool)
    Log.Checkpoint(text + ' - ' + bool);
  else
    Log.Error(text);
}

function click(obj, text){
  var value = (text == null) ? obj.Name : text;
  Log.Message('Click on - ' + value);
  Sys.HighlightObject(obj, 2);
  obj.Click();
}