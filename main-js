/*
 * Developer    : Sat Singh Rana
 * Date 		: 14-01-2020
 * Desc 		: Main Script of -- Shopify App
 */


// ssg_atcid = add to cart is done 
// ssg_cbid = check box is disabled


(function(){
	console.log('Scope initiated.... -- Shopify App');
	$(function () {

		var sgapp = {
			SITE_URL: null,
			CURRENT_URL: window.location.href,
			STORE: null,
			SG_product_id: null,
			applicable_APP_NAME_variant: null,
			is_cart_page: null,
			is_SGAPP: null,
			init: function () {
				/**
				 * Basic frontend App configuration setup.
				 */
				// Check if we are on cart page
				this._check_cart_page();
				// Append the app message holder
				this._add_message_holder();
				// Append loading
				this._append_loading();
				// Append popup and checkbox
				this._add_info_popup('$5.00(dummy text)');
				this.STORE = Shopify.shop || this.CURRENT_URL.match("https://(.*)/cart")[1];
				this._throw_message('[ Debug mode on ]');				
				this._throw_message('Background process log');				
				this._throw_message('-- app initiating...');
				$(document).ajaxComplete(function (event, xhr, ajx_settings) {
					if (sgapp.is_SGAPP==true) { return;}						
					// Run only if cart change happened					
                    var ajax_action = ajx_settings.url;
                    if ('/cart/change.js' == ajax_action) {
						var changed_data = sgapp._parse_cart_change_response(ajx_settings.data);						
						// revaluate the cart for -- shipping guarantee
						sgapp._revaluate_cart();
					}
				});

				/**
				 * Runtime Dom manipulation
				 */
				
				$( document ).on('click', '.show-sgapp-info-popup',  function (params) {
					$('.sgapp-info-popup').show();
				});
				$( document ).on('click', '.close-sgapp-info-popup',  function (params) {
					$('.sgapp-info-popup').hide();
				});

				$(document).on('click', '.sgapp_enable_disable', function (params) {
					console.log(this);
					if (!$(this).is(':checked')) {
						sgapp._remove_APP_NAME_product_from_cart();
						
					} else {
						window.location.replace('/cart');
					}
				});


				if (sgapp.CURRENT_URL.indexOf('ssg_atcid') > -1) {					
					this._throw_message('-- Shipping Guarantee add to cart is done...');
					this._get_settings_only();
					this._get_cart_subtotal();
					return;					
				}

				/**
				 * If the Shipping Guarantee checkbox is unchecked
				 */
				if (sgapp.CURRENT_URL.indexOf('ssg_cbid') > -1) {					
					this._throw_message('User discarded -- Shipping Guarantee. Check box is disabled');
					this._get_settings_only();
					this._get_cart_subtotal();
					return;					
				}

				if (null != this.is_cart_page) {
					this._throw_message('On cart page.');
					this._show_loading();
				} else {
					this._throw_message('Nothing to process.');
					return;
				}
			

				this._get_append_settings();

			},
			_remove_APP_NAME_product_from_cart: function (params) {

				/**
				 * To prevent the cart revaluation fire.
				 */

				jQuery.getJSON('/cart.js', function (cart) { 

					if (cart.items.length > 0) {
						// Get all the product ids in the cart
						$.each(cart.items, function (index, item) {							
							if ('shipping-guarantee' == item.handle) {
								sgapp.applicable_APP_NAME_variant = item.variant_id;
								return false;								
							}
						});
					}

				}).always(function (params) {
					
					sgapp.is_SGAPP = true;
					jQuery.post('/cart/change.js', { quantity: 0, id: sgapp.applicable_APP_NAME_variant }).always(function (params) {
						window.location.replace('/cart?ssg_cbid');
					});
					return;				
				});


			},
			_revaluate_cart: function (params) {

				this._throw_message('Cart change detected.');
				this._throw_message('Revaluation needed.');
				this._throw_message('Revaluating the cart...');
				this._show_loading();

				setTimeout(() => {
					sgapp._throw_message('Cart revaluated! (dummy) under development');
					console.log('hide loading')
					sgapp._hide_loading();					
				}, 2000);

				
			},
			_parse_cart_change_response: function (q_string) {
				return JSON.parse('{"' + decodeURI(q_string).replace(/"/g, '\\"').replace(/&/g, '","').replace(/=/g, '":"') + '"}');				
			},
			_add_info_popup: function (amount) {

				var cart_form = $('body').find('form[action="/cart"]');

				var submit_button = cart_form.find('input[type=submit]');

				html = '';
				html += '<div class="sgapp-info-popup" style="display:none;background:rgba(0, 0, 0, 0.8);position: fixed;top: 0;right: 0;bottom: 0;left: 0;z-index: 100000;">';
					html += '<div style="background: #ffffff;max-width: 650px;padding: 60px 50px;top: 50%;position: absolute;left: 50%;transform: translate(-50%, -50%);">';
						html += '<div class="">';
							html += '<div class="">';
								html += '<div style="text-align: center;">APP_NAME shipping guarantee provides protection for shipments that are lost, broken, or stolen. Add APP_NAME shipping&nbsp;guarantee to your cart at checkout to enable protection.</div>';
								html += '<br><br><br>';
								html += '<div style="text-align: center;"><a style="text-decoration: underline;color: #f5c22b;font-size: 18px;" href="#">User terms of use</a></div>';
								html += '<button class="close-sgapp-info-popup" style="outline:none;position: absolute;top: 10px;right: 10px;border: none;font-size: 26px;padding: 0 9px;line-height: 34px;" title="Close (Esc)" type="button" class="">Ã—</button>';
							html += '</div>';
						html += '</div>';
					html += '</div>';
				html += '</div>';

				$('body').append(html);

				var input = '';

				if (this.CURRENT_URL.indexOf('ssg_cbid') > -1) {
					input = '<input type="checkbox" class="sgapp_enable_disable" name="sgapp_enable_disable">';
				} else {
					input = '<input checked type="checkbox" class="sgapp_enable_disable" name="sgapp_enable_disable">';
				}

				submit_button.before('<div id="sgapp_checkbox" style="width:100%"><span  style="cursor: pointer;display: block;width: 100%;max-width: 140px;float:right" class="show-sgapp-info-popup"><img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAANCAMAAACuAq9NAAAAAXNSR0IB2cksfwAAAAlwSFlzAAALEwAACxMBAJqcGAAAALRQTFRF////8vLym5ubUlJSNDQ0UFBQmZmZ8PDw7u7uLS0tTk5O6+vrfn5+vLy8zs7OlZWVMTExZmZmxcXFX19f3t7ep6en9PT0cHBwaWlp+/v7S0tL19fXsbGxPT09d3d3/v7+SkpKqqqqnZ2d/Pz8kJCQlpaWsrKy+fn5bW1t4ODgLi4uLy8vi4uLjY2N2traOzs7QkJCY2Nj8fHxVlZWNjY229vbo6OjWVlZOjo6V1dXoKCg8/Pz/dn8rQAAAIZJREFUeJw1jdcWgjAQREcSlbFiQ+yCvcaG9f//y03Qebl792wBgJyntM4XinDxFV1K5b9VqrV6kHmD9Gy7SbaAtox1wm7UQ58cYGjXRhxPMCVjJO5MgtlcsMDSml5hbRljsxXs9ofA6hGIBAY4Cc7y4HIlb2lqSOXj5zaZAeH9YZ6v90fKLzK8Ct7+ftm1AAAAAElFTkSuQmCC"></span><div style="float: left;width: 100%;">'+input+' Add shipping protection for <span class="shipping-ins-price"><span class="money">'+amount+'</span></span></div> </div>')
				return;

				
			},
			_append_loading: function (params) {
				$('head').append('<style>.loader {border: 3px solid rgb(228, 228, 228);border-top: 3px solid rgb(228, 228, 228);border-radius: 50%;width: 40px;height: 40px;animation: spin 600ms linear infinite;border-bottom: 3px solid rgb(28, 34, 96);}@keyframes spin {0% { transform: rotate(0deg); }100% { transform: rotate(360deg); }}</style>');
				$('body').append('<div id="sgapp-loading" style="display:none;position: fixed;top: 0;right: 0;bottom: 0;left: 0;background: #ffffffe0;z-index: 99999;"><div style="position: absolute;left: 50%;top: 50%;transform: translate(-50%, -50%);"><div class="loader"></div></div></div>');
			},
			_show_loading: function (params) {
				$('#sgapp-loading').show();
			},
			_hide_loading : function (params) {
				$('#sgapp-loading').fadeOut(300);	
			},
			_check_cart_page: function (params) {
				if (this.CURRENT_URL.indexOf('/cart') > -1) {
					this.is_cart_page = true;
				}
			},
			_get_settings_only: function (params) {
				/**
				 * Get the store setting and append to the page.
				 */
				jQuery.ajax({
					url: sgapp.SITE_URL+'/get-store-settings.php?store='+sgapp.STORE,
					method: 'get',
					beforeSend : function name() {						
						//sgapp._throw_message('Fetch settings.');						
					},
					success: function (res) {
						$('head').append('<!--APP_NAME app settings--><script>var sgapp_settings = ' + JSON.stringify(res) + ';</script><!--APP_NAME app settings-->');
					}
				});
			},
			_get_append_settings: function (params) {
				/**
				 * Get the store setting and append to the page.
				 */
				jQuery.ajax({
					url: sgapp.SITE_URL+'/get-store-settings.php?store='+sgapp.STORE,
					method: 'get',
					beforeSend : function name() {						
						sgapp._throw_message('Fetch settings.');						
					},
					success: function (res) {						
						$('head').append('<!--APP_NAME app settings--><script>var sgapp_settings = ' + JSON.stringify(res) + ';</script><!--APP_NAME app settings-->');
						sgapp._throw_message('Settings added.');
						// Check if the APP_NAME product already exist in the cart.
						sgapp._check_the_APP_NAME_product();
					}
				});
				
			},
			_check_the_APP_NAME_product: function (params) {				
				// Get the cart data
				sgapp._throw_message('Checking for APP_NAME Shipping Guarantee.');
				this.SG_product_id = sgapp_settings.APP_NAME_product.product_id;
				this._get_the_cart_data();	
			},
			_get_cart_subtotal() {
				jQuery.getJSON('/cart.js', function (cart) {					
					if (cart.items.length > 0) {
						// Set the cart subtotal
						sgapp.CART_SUBTOTAL = ( cart.items_subtotal_price/100 );
					} else {
						sgapp.CART_SUBTOTAL = 0;
					}
				});
				
			},
			_get_the_cart_data: function () {
				jQuery.getJSON('/cart.js', function (cart) {					
					if (cart.items.length > 0) {				
						// Get all the product ids in the cart
						var cart_product_ids = [];						
						$.each(cart.items, function (index, item) {
							cart_product_ids.push(item.product_id);
						}); 
						// Set the cart subtotal						
						sgapp.CART_SUBTOTAL = ( cart.items_subtotal_price/100 );						
						sgapp._process_cart_data(cart_product_ids);
					} else {
						sgapp._throw_message('Cart is empty.');
						sgapp._hide_loading();
					}
				});
			},
			_process_cart_data: function (cart_item_ids) {
				if (cart_item_ids.includes(+this.SG_product_id)) {					
					this._throw_message('APP_NAME Shipping Guarantee already added.');
					this._hide_loading();					
				} else {
					this._throw_message('APP_NAME product is not in the cart');
					/**
					 * Check if valid APP_NAME cart.
					 */
					$.ajax({
						url: sgapp.SITE_URL + '/check-products.php',
						method: 'post',
						data: { cart_item_ids: cart_item_ids, store_id : sgapp_settings.store_id },
						beforeSend: function (params) {							
							sgapp._throw_message('Validating cart for APP_NAME Shipping Guarantee');
						},
						success: function (res) {
							/**
							 * Calculate the right variants needs to be added
							 */
							if (res[0] == 'valid_APP_NAME_cart') {
								sgapp._get_the_closest_variant();							
								jQuery.post('/cart/update.js', {
									updates: {
										[sgapp.applicable_APP_NAME_variant]: 1,
									}
								}).done(function(res) {								
									console.log(res)
								}).fail(function(res) {
									console.log(res)								  
								}).always(function(res) {							
									console.log(res)								  
									if (sgapp.CURRENT_URL.indexOf('ssg_atcid')==-1) {
										window.location.replace(sgapp.CURRENT_URL+'?ssg_atcid');
									}
								});								
							}
							if (res[0] == 'invalid_APP_NAME_cart') {
								console.log('invalid_APP_NAME_cart')								
								sgapp._throw_message('<span style="color:red">1 or more products are in cart without APP_NAME enabled.<br> cart can\'t be processed.</span>');
								
								// hide the check box.
								$(document).find('#sgapp_checkbox').hide();
								sgapp._hide_loading();								
								
							}
							if (res[0] == 'APP_NAME_not_applicable') {
								sgapp._throw_message('<span style="color:red">No product in cart APP_NAME enabled. Shipping Guarantee Not Applicable</span>');
								// hide the check box.
								$(document).find('#sgapp_checkbox').hide();
								sgapp._hide_loading();
								console.log( 'APP_NAME_not_applicable' )									
							}							
							return;
						}
					});
				}
			},
			_add_message_holder: function (msg) {
				$('body').append('<div id="sgapp_message_holder" style="z-index: 100000;position: fixed;top: 100px;left: 50px;padding: 20px;background: #e4e4e4;box-shadow: -5px 0px 0 #557b97;font-size: 14px;"></div>');	
			},
			_throw_message: function (msg) {
				$('#sgapp_message_holder').append('<div>'+msg+'<hr style="margin: 0;border-bottom-color: #557b97;"></div>');	
			},
			_get_the_closest_variant: function () {

				var variants 		= sgapp_settings.APP_NAME_product.variants;
				var percentage  	= sgapp_settings.percentage							
				var fee_will_be 	= (this.CART_SUBTOTAL * (+sgapp_settings.percentage)) / 100;
				var all_price_array = [];				
				for (let index = 0; index < variants.length; index++) {				
					const c_variant = variants[index];
					all_price_array.push( c_variant.price );
				}

				/**
				 * Min price
				 */
				var min_price = all_price_array[0];
				/**
				 * Max price
				 */				
				var max_price = all_price_array[ (all_price_array.length -1)  ];				
				// If fee is less then minimum APP_NAME variant.
				if (fee_will_be < min_price) {
					return this.applicable_APP_NAME_variant = variants[0].id;//'min_variant';
				}				
				// If fee is more then maximum APP_NAME variant.
				if (fee_will_be > max_price) {
					return this.applicable_APP_NAME_variant = variants[(variants.length-1)].id;//'max_variant';
				}
				for (let index = 0; index < variants.length; index++) {
					const c_variant = variants[index];
					console.log('Variant price :', c_variant.price, ' Fee will be : ', fee_will_be);					
					if (c_variant.price >= fee_will_be) {
						this.applicable_APP_NAME_variant = c_variant.id;
						break;
					}
					if (c_variant.price >= fee_will_be) {					
						this.applicable_APP_NAME_variant = c_variant.id;
						break;
					}
				};
				return;
			}
		}
		/**
		 * Initiate the cart page code
		 */
		sgapp.init();
	});
})();
