# WP-Register-Form
Add new additional fields to default registration form in wordpress.
Add this code on your theme function.php

```


function mytheme_register_form() 
{
    $first_name = ( ! empty( $_POST['first_name'] ) ) ? trim( $_POST['first_name'] ) : '';
    $last_name = ( ! empty( $_POST['last_name'] ) ) ? trim( $_POST['last_name'] ) : '';

    ?>
    
    <p>
        <label for="first_name"><?php _e( 'First  name', 'mytheme' ) ?><br />
                    <input type="text" name="first_name" id="first_name" value="<?php echo esc_attr( wp_unslash( $first_name ) ); ?>" class="regular-text">
        </label>
           </p>

            <p>
        <label for="last_name"><?php _e( 'Last  name', 'mytheme' ) ?><br />
                    <input type="text" name="last_name" id="last_name" value="<?php echo esc_attr( wp_unslash( $last_name ) ); ?>" class="regular-text">
        </label>
           </p>
    <?php
}
add_action( 'register_form', 'mytheme_register_form' );




add_action( 'register_form', 'crf_registration_form' );
function crf_registration_form() {

	$year = ! empty( $_POST['year_of_birth'] ) ? intval( $_POST['year_of_birth'] ) : '';

	?>
	<p>
		<label for="year_of_birth"><?php esc_html_e( 'Year of birth', 'crf' ) ?><br/>
			<input type="number"
			       id="year_of_birth"
			       name="year_of_birth"
			       value="<?php echo esc_attr( $year ); ?>"
			       class="input"
			/>
		</label>
	</p>
	<?php
}


add_filter( 'registration_errors', 'crf_registration_errors', 10, 3 );
function crf_registration_errors( $errors, $sanitized_user_login, $user_email ) {

	if ( empty( $_POST['year_of_birth'] ) ) {
		$errors->add( 'year_of_birth_error', __( '<strong>ERROR</strong>: Please enter your year of birth.', 'crf' ) );
	}

	if ( ! empty( $_POST['year_of_birth'] ) && intval( $_POST['year_of_birth'] ) < 1900 ) {
		$errors->add( 'year_of_birth_error', __( '<strong>ERROR</strong>: You must be born after 1900.', 'crf' ) );
	}

	return $errors;
}



add_action( 'user_register', 'crf_user_register', 10, 1 );
function crf_user_register( $user_id ) {


	if ( ! empty( $_POST['year_of_birth'] ) ) {
		update_user_meta( $user_id, 'year_of_birth', intval( $_POST['year_of_birth'] ) );
	}
	if ( ! empty( $_POST['first_name'] ) ) {
		update_user_meta( $user_id, 'first_name',  $_POST['first_name']  );
	}

	if ( ! empty( $_POST['last_name'] ) ) {
		update_user_meta( $user_id, 'last_name',  $_POST['last_name']  );
	}
}


add_action( 'user_new_form', 'crf_admin_registration_form' );
function crf_admin_registration_form( $operation ) {
	if ( 'add-new-user' !== $operation ) {
		// $operation may also be 'add-existing-user'
		return;
	}

	$year = ! empty( $_POST['year_of_birth'] ) ? intval( $_POST['year_of_birth'] ) : '';

	?>
	<h3><?php esc_html_e( 'Personal Information', 'crf' ); ?></h3>

	<table class="form-table">
		<tr>
			<th><label for="year_of_birth"><?php esc_html_e( 'Year of birth', 'crf' ); ?></label> <span class="description"><?php esc_html_e( '(required)', 'crf' ); ?></span></th>
			<td>
				<input type="number"
			       id="year_of_birth"
			       name="year_of_birth"
			       value="<?php echo esc_attr( $year ); ?>"
			       class="regular-text"
				/>
			</td>
		</tr>
	</table>
	<?php
}



add_action( 'user_profile_update_errors', 'crf_user_profile_update_errors', 10, 3 );
function crf_user_profile_update_errors( $errors, $update, $user ) {
	if ( $update ) {
		return;
	}

	if ( empty( $_POST['year_of_birth'] ) ) {
		$errors->add( 'year_of_birth_error', __( '<strong>ERROR</strong>: Please enter your year of birth.', 'crf' ) );
	}

	if ( ! empty( $_POST['year_of_birth'] ) && intval( $_POST['year_of_birth'] ) < 1900 ) {
		$errors->add( 'year_of_birth_error', __( '<strong>ERROR</strong>: You must be born after 1900.', 'crf' ) );
	}
}

add_action( 'show_user_profile', 'crf_show_extra_profile_fields' );
add_action( 'edit_user_profile', 'crf_show_extra_profile_fields' );

function crf_show_extra_profile_fields( $user ) {
	?>
	<h3><?php esc_html_e( 'Personal Information', 'crf' ); ?></h3>

	<table class="form-table">
		<tr>
			<th><label for="year_of_birth"><?php esc_html_e( 'Year of birth', 'crf' ); ?></label></th>
			<td>
				<input type="number"
			       id="year_of_birth"
			       name="year_of_birth"
			       value="<?php echo esc_html( get_the_author_meta( 'year_of_birth', $user->ID ) ); ?>"
			       class="regular-text"
				/>

				<?php //echo esc_html( get_the_author_meta( 'year_of_birth', $user->ID ) ); ?>

			</td>
		</tr>
	</table>
	<?php
}


```
