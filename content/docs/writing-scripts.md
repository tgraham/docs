--- 
title: Writing Scripts
---

## What is a Script?

StackFu scripts are composed by four different components: controls, requirements, executions and validations. One script is represented by a folder on the file system containing YAML and ERB files.

Each script has a script.yml descriptor and two subfolders: config and executables. The config folder contains four configuration files: 01-controls.yml, 02-requirements.yml, 03-executions.yml and 04-validations.yml.

Here's a typical folder tree for a script:

	my_script/
		config/
			01-controls.yml
			02-requirements.yml
			03-executions.yml
			04-validations.yml
		executables/
			install_my_script.sh.erb
		script.yml
	
## The script descriptor

The file script.yml describes your script. It has attributes like name, description and tags. Here's an example:

	---
	type: script
	name: my_script
	description: Sets up my Ubuntu environment
	tags: [dotfiles, ubuntu]

### `script.yml` attributes

- **type:** should always contain the string "script"		
- **name:** the name of your script -- try to separate words by either underscore (_) or a dash (-)	
- **description:** a longer explanation of what your script does	
- **tags:** comma-separated tags for your script 	

## Configuration files

The configuration files are the ones that reside on the `config/` folder. Every script must have four files, even if with no contents.

### `01-controls.yml`

Controls are the input parameters that your script takes. Each control has at least a name, a label and a type. A simple example of a control is:

	controls:
	- name: path
	  label: Path to Install
	  type: Textbox

Free-text control types, like Textbox, Numericbox or Password can take one or more validation parameters, that allow customization of which values are expected on the control.

On the other hand, list-based controls like Radio and Combobox require a list of options. 

Here's one example of an hypotetical config file with multiple controls of multiple types:

	--- 
	controls: 
	- name: grade
	  label: Grade
	  type: Textbox
	  required: "true"
	  validations: 
	    maxlength: 1
	    matches: ^[A|B|C]$
	  validation_messages: 
	    maxlength: must have 1 or less characters!
	    matches: must be A, B or C
	- name: pass
	  label: Pass?
	  type: Radio
	  required: "true"
	  options: 
	  - ['y', 'Yes]
	  - ['n', 'No']
	
Fields for each control:

- **type:** the type of the control that will be rendered to the user. Accepted types: `Textbox`, `Numericbox`, `Password`, `Checkbox`, `Combobox`, `Radio`.    

- **name:** control name, words must be separated by underscores	    

- **label:** a label for the control 	    

- **required:** indicates whether the control must have a value. Accepted values: `true` or `false`.	    

- **validations:** a list of validations to apply to this control. The format of this field is always **`type: value`**.     


### Validations

Here are listed all the possible validations:

<table> 
  <thead> 
    <tr> 
      <th class="validation">Validation</th> 
      <th class="description">Description</th> 
      <th class="example">Example</th> 
    </tr> 
  </thead> 
  <tbody> 
    <tr> 
      <td class="validation">minlength</td> 
      <td class="description">Minimum length of input</td> 
      <td class="example"><code>minlength: 8</code></td> 
    </tr> 
    <tr> 
      <td class="validation">maxlength</td> 
      <td class="description">Maximum length of input</td> 
      <td class="example"><code>maxlength: 20</code></td> 
    </tr> 
    <tr> 
      <td class="validation">rangelength</td> 
      <td class="description">Array with min and max length of input</td> 
      <td class="example"><code>rangelength: [8, 20]</code></td> 
    </tr> 
    <tr> 
      <td class="validation">min</td> 
      <td class="description">Requires a given numeric minimum</td> 
      <td class="example"><code>min: 1</code></td> 
    </tr> 
    <tr> 
      <td class="validation">max</td> 
      <td class="description">Requires a given numeric maximum</td> 
      <td class="example"><code>max: 9</code></td> 
    </tr> 
    <tr> 
      <td class="validation">range</td> 
      <td class="description">Requires input to be in a given numeric min-max range</td> 
      <td class="example"><code>range: [1, 9]</code></td> 
    </tr> 
    <tr> 
      <td class="validation">email</td> 
      <td class="description">Value must have the format of an email</td> 
      <td class="example"><code>email: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">url</td> 
      <td class="description">Value must have the format of an url</td> 
      <td class="example"><code>url: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">date</td> 
      <td class="description">Requires a date<sup id="fnref:dateobs"><a href="#fn:dateobs" rel="footnote">1</a></sup></td> 
      <td class="example"><code>url: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">dateISO</td> 
      <td class="description">Requires an ISO formatted date</td> 
      <td class="example"><code>url: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">dateISO</td> 
      <td class="description">Requires an ISO formatted date</td> 
      <td class="example"><code>url: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">number</td> 
      <td class="description">Value must be a decimal number</td> 
      <td class="example"><code>number: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">digits</td> 
      <td class="description">Requires digits only</td> 
      <td class="example"><code>digits: true</code></td> 
    </tr> 
    <tr> 
      <td class="validation">equalTo</td> 
      <td class="description">Makes the input match another input<sup id="fnref:equalnotes"><a href="#fn:equalnotes" rel="footnote">2</a></sup></td> 
      <td class="example"><code>equalTo: #params_another_one</code></td> 
    </tr> 
    <tr> 
      <td class="validation">matches</td> 
      <td class="description">When everything else fails, you can use matches and pass a Regex</td> 
      <td class="example"><code>matches: ^A</code></td> 
    </tr> 
  </tbody> 
</table>

a[^dateobs]
a[^equalnotes]

[^dateobs]: Only the format must be valid, not the actual date, e.g. `30/30/2008` is a valid date. 	

[^equalnotes]: The attribute of `equalTo` must be a CSS selector (like jQuery), and our controls ids are always prepended with `params_`, so a control called `path_name` becomes `params_path_name`.