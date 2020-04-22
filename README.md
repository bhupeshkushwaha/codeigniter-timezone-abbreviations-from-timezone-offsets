# codeigniter-timezone-abbreviations-from-timezone-offsets
Timezone Abbreviations From Timezone Offsets
~~~
/**
	 * Set logged in user default time zone by session
	 * 
	 * @return void
	 */
	if(!function_exists('set_custom_timezone')){
		function set_custom_timezone() {
			$ci = &get_instance();
      			$ci->load->library('session');

			$loggedInData = $ci->session->userdata('loggedin');

			if (isset($loggedInData['timezone']) && !empty($loggedInData['timezone']) ) {
				date_default_timezone_set($loggedInData['timezone']);
			}
		}
	}

	/**
	 * Convert date according to timezone
	 * 
	 * @param date $date_time
	 * @return string
	 */
	if(!function_exists('dateFromTimezone')){
		function dateFromTimezone($date_time) {
			$ci = &get_instance();
      			$ci->load->library('session');

			$loggedInData = $ci->session->userdata('loggedin');

			if(isset($loggedInData['role']) && $loggedInData['role'] == 2) {
				$utcDate = DateTime::createFromFormat(
					'Y-m-d H:i:s',
					$date_time,
					new DateTimeZone('UTC')
				);

				return	$utcDate->format('Y-m-d H:i:s');		
			}

			if (isset($loggedInData['timezone']) && !empty($loggedInData['timezone']) ) {
				$utcDate = DateTime::createFromFormat(
					'Y-m-d H:i:s',
					$date_time,
					new DateTimeZone('UTC')
				);
				
				$localTimeDate = clone $utcDate;
				$localTimeDate->setTimeZone(new DateTimeZone($loggedInData['timezone']));

				return $localTimeDate->format('Y-m-d H:i:s');
			}

			return $date_time;
		}
	}

	/**
	 * Convert date according to timezone for API
	 * 
	 * @param date $date_time
	 * @param string $timezone
	 * @return string
	 */
	if(!function_exists('dateFromTimezoneForApi')){
		function dateFromTimezoneForApi($date_time, $timezone) {
			if (isset($timezone) && !empty($timezone) ) {
				$timezoneArray = explode('/', $timezone);

				if( count($timezoneArray) == 1) {
					$timezone = findTimezone($timezone, $date_time);
				} 

				$utcDate = DateTime::createFromFormat(
					'Y-m-d H:i:s',
					$date_time,
					new DateTimeZone('UTC')
				);
				
				$localTimeDate = clone $utcDate;
				$localTimeDate->setTimeZone(new DateTimeZone($timezone));

				return $localTimeDate->format('Y-m-d H:i:s');
			}

			return $date_time;
		}
	}

	/**
	 * Fine timezone abbreviations from timezone offsets 
	 * 
	 * @param string $offset
	 * @param string $date
	 * @param string $countries
	 * return mix
	 */
	function findTimezone($offset, $date = 'now', $countries = DateTimeZone::ALL) {
	    foreach (DateTimeZone::listIdentifiers($countries) as $timezone) {
		$datetime = new DateTime($date, new DateTimeZone($timezone));

		// find a timezone matching the offset and abbreviation
		if ($offset == $datetime->format('T')) {
		    return $timezone;
		}
	    }
	}
  ~~~
