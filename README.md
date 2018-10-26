# replaceXMLHttpReq

(function () {
    "use strict";

    // сохраним оригинальный объект, т.к. без него не сможем слат запросы
    var XHR = window.XMLHttpRequest;

    window.XMLHttpRequest = function () {

        // создаем экземпляр оригинала
        var o = new XHR(),
            t = this,
            reassignAllProperties = function reassign() {
                t.readyState = o.readyState;
                t.responseText = o.responseText;
                t.responseXML = o.responseXML;
                t.status = o.status;
                t.statusText = o.statusText;
            };
        
        t.readyState = 0;
        t.responseText = "";
        t.responseXML = null;
        t.status = null;
        t.statusText = "";

        // просто подменим все методы, не меняя никак поведение
        // но добавим вызов reassignAllProperties() т.к. после
        // вызова любого из методов может быть изменено какое-то св-во
        t.open = function open() {
            o.open.apply(o, arguments);
            reassignAllProperties();
        };

        t.send = function send() {
            o.send.apply(o, arguments);
            reassignAllProperties();
        };

        t.abort = function abort() {
            o.abort();
            reassignAllProperties();
        };

        t.setRequestHeader = o.setRequestHeader;

        t.overrideMimeType = o.overrideMimeType;

        t.getResponseHeader = o.getResponseHeader;

        t.getAllResponseHeaders = o.getAllResponseHeaders;

        t.onreadystatechange = function () {};


        o.onreadystatechange = function onReady() {
            reassignAllProperties();
            t.onreadystatechange();
        };

    };

})();
