function ptFormValidate(f, errorMessage)
{
    if (jQuery && f.elements['_check_platinum_captcha'] && f.elements['_check_platinum_captcha'].value != '')   //ho jQuery disponibile...posso fare chiamata ajax per verificare il captcha...
    {
        var captchaVal = f.elements[f.elements['_check_platinum_captcha'].value].value;
        if (!captchaVal)
            return giveAlert(f.elements[f.elements['_check_platinum_captcha'].value], errorMessage);

        $.post('/nodes/check_captcha.jsp', $(f).serialize(), function(data) {
            if (data.trim() == 'ok')
            {
                f.elements['_check_platinum_captcha'].value = '';   //svuoto, cosi' non viene rifatto il test lato server (dato che il captcha e' gia' stato validato!)
                $(f).submit();
            }
            else
            {
                $('#platinumCaptchaImg').attr('src', '/services/jcaptcha.jpg?'+(new Date().getTime()));
                giveAlert(f.elements[f.elements['_check_platinum_captcha'].value], errorMessage);
                f.elements[f.elements['_check_platinum_captcha'].value].focus();
                //f.elements[f.elements['_check_platinum_captcha'].value].select();
            }
        });
        return false;
    }
    else 
    if (jQuery && f.elements['g-recaptcha-response'])   //ho jQuery disponibile...posso fare chiamata ajax per verificare il captcha...
    {
        var captchaVal = f.elements['g-recaptcha-response'].value;
        if (!captchaVal)
            return giveAlert(f.elements['g-recaptcha-response'], errorMessage);

        $.post('/nodes/check_captcha.jsp', $(f).serialize(), function(data) {
            if (data.trim() == 'ok')
            {
                f.elements['g-recaptcha-response'].remove();   //svuoto, cosi' non viene rifatto il test lato server (dato che il captcha e' gia' stato validato!)
                $(f).submit();
            }
            else
            {
                giveAlert(f.elements['g-recaptcha-response'], errorMessage);
            }
        });
        return false;
    }
    else
        return _ptFormValidate(f, errorMessage);
}

function _ptFormValidate(f, errorMessage)
{
    var radiosRequired = new Object();
    var checkboxesRequired = new Object();
    for (var i = 0; i < f.elements.length; i++)
    {
        var e = f.elements[i];
        var t = e.type;
        if (t == "hidden" || t == "submit" || t == "button")
            continue;
        var n = e.className;
        var m = errorMessage;

        if (t == "text" || t == "password" || t == "file" || t == "select-one" || t == "textarea")
        {
            if (hasRequiredFlag(n))
            {
                if (isEmpty(e, m))
                    return false;
            }

            var minLength = hasMinimunLengthFlag(n);
            if (minLength >= 1)
            {
                if (isShorterThan(e, m, minLength))
                    return false;
            }

            if (hasNumberFlag(n))
            {
                if (!isNumber(e, m))
                    return false;
            }

            if (hasEmailFlag(n))
            {
                if (!isEmail(e, true, m))
                    return false;
            }
        }

        if (t == "file")
        {
            if (hasRequiredFlag(n) || e.value)
            {
                var exts = '*.*';
                if (f[e.name + '_exts'] && f[e.name + '_exts'].value)
                    exts = f[e.name + '_exts'].value;
                var allFiles = (exts.indexOf('*.*') >= 0);   //in mezzo c'e' *.*, che implica che tutti i file sono validi!
                if (!allFiles)
                {
                    exts = exts.replace(',', ';');
                    exts = exts.split(';');
                    for (var key in exts)
                    {
                        exts[key] = exts[key].trim().substring(2).toLowerCase();
                    }
                    if (!checkFile(e, exts, allFiles))
                        return giveAlert(e, m);
                }
            }
        }

        if (t == "checkbox")
        {
            if (hasRequiredFlag(n))
                checkboxesRequired[e.name] = true;
        }

        if (t == "radio")
        {
            if (hasRequiredFlag(n))
                radiosRequired[e.name] = true;
        }
    }

    for (var k in checkboxesRequired)
    {
        if (checkboxesRequired[k])
        {
            var ok = false;
            var checkboxGroup = f.elements[k];
            if (checkboxGroup.length)  //se e' definito il campo "length", e' un array!
            {
                for (i = 0; i < checkboxGroup.length; i++)
                {
                    if (checkboxGroup[i].checked)
                    {
                        ok = true;
                        break;
                    }
                }
                if (!ok)
                    return giveAlert(checkboxGroup[0], m);
            }
            else
            {
                if (!checkboxGroup.checked)
                    return giveAlert(checkboxGroup, m);
            }
        }
    }

    for (k in radiosRequired)
    {
        if (radiosRequired[k])
        {
            var ok = false;
            var radioGroup = f.elements[k];
            for (i = 0; i < radioGroup.length; i++)
            {
                if (radioGroup[i].checked)
                {
                    ok = true;
                    break;
                }
            }
            if (!ok)
                return giveAlert(radioGroup[0], m);
        }
    }

    return true;
}

//for backward compatibility...
if (typeof formValidate == 'undefined')
{
    formValidate = ptFormValidate;
}

function isEmpty(e, m)
{
    if (e.type != "file")
        e.value = trim(e.value);
    if (e.value == "")
        return !giveAlert(e, m);

    return false;
}

function isNumber(e, m)
{
    e.value = trim(e.value);
    var i = parseFloat(e.value);
    if (isNaN(i))
        return giveAlert(e, m);
    return true;
}

function isEmail(e, empty, m)
{
    e.value = trim(e.value);
    if ((e.value == "") && empty)
        return true;

    var re = /^.+@.+\..+$/;
    if (!re.test(e.value))
        return giveAlert(e, m);
    return true;
}

function isShorterThan(e, m, minLength)
{
    e.value = trim(e.value);
    if (e.value.length < minLength)
        return !giveAlert(e, m);

    return false;
}

function giveAlert(e, m)
{
    e.focus();
    var t = e.title;
    if (t == null || t == "")
        t = e.name;
    alert(m.replace("{title}", t));
    return false;
}

function hasFlag(n, f)
{
    var flag = "__" + f + "__";
    return (n.lastIndexOf(flag) >= 0);
}

function hasRequiredFlag(n)
{
    return hasFlag(n, "noempty");
}

function hasEmailFlag(n)
{
    return hasFlag(n, "email");
}

function hasNumberFlag(n)
{
    return hasFlag(n, "numeric");
}

function hasMinimunLengthFlag(n)
{
    var flag = "__minlength";
    var x = n.lastIndexOf(flag);
    if (x < 0)
        return -1;
    var length = parseInt(n.substring(n.indexOf('-', x + flag.length) + 1, n.indexOf('__', x + flag.length) + 1));
    return length;
}

function trim(s)
{
    var tmp = s.replace(/^[\s\u00A0]*/, '').replace(/[\s\u00A0]*$/, '');
    return tmp;
}

function openWindow(pageToOpen, title, width, height, parameters)
{
    scH = screen.availHeight;
    scW = screen.availWidth;
    t = (scH / 2) - (height / 2);
    l = (scW / 2) - (width / 2);
    var w = window.open(pageToOpen, title, 'width=' + width + ',height=' + height + ',left=' + l + ',top=' + t + ',' + parameters);
    w.focus();
    return w;
}

function compileCheckboxesList(form)
{
    var checkboxesList = '';

    for (var i = 0; i < form.elements.length; i++)
    {
        var e = form.elements[i];
        var t = e.type;
        if (t == "checkbox" || t == 'radio')
        {
            checkboxesList += e.name + ',';
        }
    }

    if (checkboxesList.charAt(checkboxesList.length - 1) == ',')
    {
        checkboxesList = checkboxesList.substring(0, checkboxesList.length - 1);
    }
    form.platinumCheckboxesList.value = checkboxesList;
}

if (typeof String.prototype.trim == 'undefined')
{
    String.prototype.trim = function () {
        return this.replace(/^\s\s*/, '').replace(/\s\s*$/, '');
    }
}

function checkFile(fileInput, exts, allFiles)
{
    if (allFiles)
        return true;
    var f = fileInput.value;
    var i = f.lastIndexOf('.');
    var f_ext;
    if (i < 0)
        f_ext = '';
    else
        f_ext = f.substring(i + 1).toLowerCase();
    for (var key in exts)
    {
        if (f_ext == exts[key])
        {
            return true;
        }
    }

    return false;
}

function Platinum() {
}

Platinum.setCookie = function (name, value, expires, domain, path, secure) {
    if (value === null) {
        value = '';
        expires = -1;
    }
    var expires = '';
    if (expires && (typeof expires == 'number' || expires.toUTCString)) {
        var date;
        if (typeof expires == 'number') {
            date = new Date();
            date.setTime(date.getTime() + (expires * 24 * 60 * 60 * 1000));
        } else {
            date = expires;
        }
        expires = '; expires=' + date.toUTCString(); // use expires attribute, max-age is not supported by IE
    }
    // CAUTION: Needed to parenthesize options.path and options.domain
    // in the following expressions, otherwise they evaluate to undefined
    // in the packed version for some reason...
    var path = path ? '; path=' + (path) : '';
    var domain = domain ? '; domain=' + (domain) : '';
    var secure = secure ? '; secure' : '';
    document.cookie = [name, '=', encodeURIComponent(value), expires, path, domain, secure].join('');
}

Platinum.getCookie = function (name) {
    var cookieValue = null;
    if (document.cookie && document.cookie != '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = Platinum.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) == (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}

Platinum.trim = function trim(s)
{
    var tmp = s.replace(/^[\s\u00A0]*/, '').replace(/[\s\u00A0]*$/, '');
    return tmp;
}

