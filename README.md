# ToolTip
Tempermonkey Script for getting a tool tip for keywords with descriptions

// ==UserScript==
// @name          ToolTip
// @namespace     ChanduRamG
// @author        ChanduRamG
// @run-at        document-end
// @version       1
// @description   Shows a tooltip when text is selected
// @include       https://*
// @grant         GM_registerMenuCommand
// @grant         GM_setValue
// @grant         GM_getValue
// ==/UserScript==

(function() {
  'use strict';

  if (window.self !== window.top) {
    return;
  }

  const keywords = {
chandu:'name chandu is derived from moon and its a hindhu name etc.',
ravi:'Ravi is a male name. It means "sun" in Sanskrit.',


  };

  function setUserPref(varName, defaultVal, menuText, promptText, separator) {
    GM_registerMenuCommand(menuText, function() {
      const val = prompt(promptText, GM_getValue(varName, defaultVal)).trim().replace(/\s{2,}/g, ' ');
      if (val === null || val === GM_getValue(varName, defaultVal)) {
        return;
      }
      const formattedVal = separator ? val.replace(new RegExp(`\\s*${separator}+\\s*`, 'g'), separator).replace(new RegExp(`(?:^${separator}+|${separator}+$)`, 'g'), '') : val;
      GM_setValue(varName, formattedVal);
      location.reload();
    });
  }

  function THmoa_showTooltip(el) {
    const tooltip = document.createElement('div');
    tooltip.classList.add('THmoa');
    tooltip.style.cssText = `
      position: absolute;
      z-index: 9999;
      display: none;
      padding: 10px;
      border: 1px solid #ccc;
      background: #fff;
      border-radius: 5px;
    `;

    el.addEventListener('mouseup', handleSelection);

      function handleSelection(e) {
          const sel = window.getSelection().toString().trim().toLowerCase();
          if (sel && !sel.match(/^\s+$/) && keywords.hasOwnProperty(sel)) {
              tooltip.innerHTML = `<strong>${sel}</strong><br/>${keywords[sel]}`;
              tooltip.style.cssText += `
      display: block;
      top: ${e.pageY + 10}px;
      left: ${e.pageX + 10}px;
    `;
              document.body.appendChild(tooltip);
          } else if (!tooltip.contains(e.target)) { // check if selection is not within tooltip element
              tooltip.style.display = 'none';
          }
      }
  }

  setUserPref('THmoa_keywords', JSON.stringify(keywords), 'Edit Keywords', 'Enter a comma-separated list of keywords and their descriptions, in the format "keyword:description". Use double colons (::) if your descriptions may contain colons.');
  setUserPref('THmoa_separator', ',', 'Edit Separator', 'Enter a character or string to use as the separator between keywords and descriptions.');

  THmoa_showTooltip(document.body);
})();
